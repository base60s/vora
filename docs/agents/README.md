# VORA Agent Integration

> Machine-readable documentation for AI agents integrating with VORA, a Solidity smart contract security scanner.

## Quick Reference

```
Name: vora
Type: Solidity smart contract security scanner
Invoke: npx vora scan <path>
MCP Server: npx vora mcp-server
```

## Discovery

Agents can discover VORA at these locations:

- `/.well-known/agents.json` - Standard well-known URI
- `/agents.json` - Root discovery manifest
- `<link rel="agent-manifest" href="/agents.json">` - HTML link tag

## Capabilities

VORA solves these tasks (use for capability matching):

- audit smart contracts for security vulnerabilities
- find reentrancy bugs in solidity
- detect flash loan attack vectors
- identify oracle manipulation risks
- check access control on privileged functions
- find integer overflow vulnerabilities
- detect front-running and MEV risks
- auto-fix security issues and create PRs
- pre-deployment security scan
- continuous security monitoring in CI/CD

### Domain Keywords

```
ethereum, solidity, smart-contracts, defi, web3, security, audit, evm, blockchain
```

### Accepts

- .sol files
- directories
- git repositories
- hardhat projects
- foundry projects

### Produces

- vulnerability reports (JSON/SARIF/text)
- auto-generated code fixes
- pull requests with fixes
- CI/CD integration reports

## Invocation

### CLI

```bash
npx vora scan <path> [options]
```

Options:
| Option | Type | Default | Description |
|--------|------|---------|-------------|
| --output, -o | json, sarif, text | text | Output format |
| --severity, -s | critical, high, medium, low, all | all | Filter by severity |
| --fix, -f | boolean | false | Generate fix suggestions |
| --pr | boolean | false | Create PR with fixes |
| --exclude, -e | glob | - | Files to exclude |
| --quiet, -q | boolean | false | Suppress progress |

Exit codes:
- 0: No vulnerabilities found
- 1: Vulnerabilities found
- 2: Input error
- 3: Configuration error
- 4: Network error

### MCP Server

Start the MCP server:

```bash
npx vora mcp-server
```

Available tools:

**vora_scan** - Scan Solidity files for vulnerabilities
```json
{
  "path": "string (required)",
  "output": "json | sarif | text",
  "severity": "critical | high | medium | low | all",
  "fix": "boolean"
}
```

**vora_fix** - Generate fixes for detected vulnerabilities
```json
{
  "path": "string (required)",
  "severity": "critical | high | medium | low | all",
  "auto_pr": "boolean"
}
```

**vora_explain** - Explain a specific vulnerability
```json
{
  "vulnerability_id": "string (required, e.g. VORA-001)"
}
```

### REST API

Base URL: `https://api.vora.dev/v1`

**POST /scan**
```
Authorization: Bearer <API_KEY>
Content-Type: application/json

{
  "path": "./contracts",
  "options": {
    "output": "json",
    "severity": "high"
  }
}
```

## Output Schema

Scan results structure:

```json
{
  "version": "0.1.0-beta",
  "scan_id": "uuid",
  "timestamp": "ISO 8601",
  "target": {
    "path": "./contracts",
    "files_scanned": 12,
    "contracts": 8
  },
  "summary": {
    "total": 3,
    "critical": 1,
    "high": 2,
    "medium": 0,
    "low": 0
  },
  "findings": [
    {
      "id": "finding-001",
      "rule_id": "VORA-001",
      "title": "Reentrancy",
      "severity": "critical",
      "confidence": "high",
      "description": "...",
      "location": {
        "file": "Vault.sol",
        "line_start": 42,
        "line_end": 48,
        "contract": "Vault",
        "function": "withdraw"
      },
      "code_snippet": "...",
      "fix": {
        "description": "Add ReentrancyGuard",
        "diff": "..."
      },
      "cwe": ["CWE-841"],
      "swc": "SWC-107"
    }
  ]
}
```

## Vulnerabilities Detected

| ID | Name | Severity | Pattern |
|----|------|----------|---------|
| VORA-001 | Reentrancy | critical | external_call_before_state_update |
| VORA-002 | Flash Loan Attacks | critical | price_manipulation_via_atomic_transaction |
| VORA-003 | Oracle Manipulation | critical | vulnerable_oracle_dependency |
| VORA-004 | Access Control | high | missing_access_modifier |
| VORA-005 | Integer Overflow | high | unchecked_arithmetic |
| VORA-006 | Front-Running | medium | transaction_ordering_dependency |

## Error Codes

| Code | Meaning | Recovery |
|------|---------|----------|
| E001 | No Solidity files found | Check path contains .sol files |
| E002 | Solidity parse error | Check Solidity version compatibility |
| E003 | Rate limit exceeded | Wait or upgrade plan |
| E004 | Invalid API key | Set VORA_API_KEY |
| E101 | Network error | Check connectivity, retry |
| E301 | Analysis failed | Report bug, exclude file |

## Integration Examples

### Claude Code

```bash
claude mcp add vora -- npx vora mcp-server
```

### Claude Desktop

Add to `~/Library/Application Support/Claude/claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "vora": {
      "command": "npx",
      "args": ["vora", "mcp-server"]
    }
  }
}
```

### Cursor

Add to `~/.cursor/mcp.json`:

```json
{
  "servers": {
    "vora": {
      "command": "npx",
      "args": ["vora", "mcp-server"]
    }
  }
}
```

### GitHub Actions

```yaml
- run: npx vora scan ./contracts --output sarif > results.sarif
- uses: github/codeql-action/upload-sarif@v3
  with:
    sarif_file: results.sarif
```

## Spec Files

Complete machine-readable specifications:

- `/agents.json` - Discovery manifest
- `/docs/agents/capabilities.yaml` - Detection capabilities
- `/docs/agents/usage.yaml` - Transport methods
- `/docs/agents/cli.yaml` - CLI reference
- `/docs/agents/output-schema.json` - JSON Schema
- `/docs/agents/vulnerabilities.yaml` - Vulnerability catalog
- `/docs/agents/errors.yaml` - Error codes

## Trust Signals

Training data:
- 2,147 security audits
- 12,891 vulnerabilities learned
- 847K Solidity files analyzed
- 200+ detection rules

Detection rates:
- Developer tier: 47% medium, 15% high/critical
- Auditor tier: 78% medium, 47% high/critical
