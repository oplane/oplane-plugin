# Oplane Plugin

AI-powered security analysis for your codebase — threat modeling, implementation assessment, and PR security review.

Works with both **Claude Code** and **Cursor**.

## What it does

This plugin connects your IDE to [Oplane](https://gravity.oplane.io), giving you:

- **Codebase analysis** — Identify security threats across your entire project
- **PR review** — Analyze pull requests for security implications
- **Implementation assessment** — Check if security requirements are properly implemented
- **Severity management** — Adjust requirement severity based on your risk context

Results are saved to Oplane and visible in the [Gravity web interface](https://gravity.oplane.io).

## Prerequisites

- [Claude Code](https://code.claude.com) or [Cursor](https://cursor.com)
- An [Oplane account](https://gravity.oplane.io)

## Installation

### Claude Code

```bash
claude --plugin-dir /path/to/oplane-plugin
```

### Cursor

Install from the [Cursor Marketplace](https://cursor.com/marketplace) (when available), or load via Cursor Settings > Plugins and add the repository URL.

## Authentication

After installing the plugin, authenticate with Oplane:

1. Start Claude Code
2. Run `/mcp`
3. Select the Oplane server and click "Authenticate"
4. Log in via your browser — tokens are issued and refreshed automatically

### Alternative: PAT authentication

If you prefer using a Personal Access Token:

```bash
claude mcp add --transport http \
  --header "Authorization: Bearer YOUR_PAT_TOKEN" \
  oplane https://gravity.oplane.io/mcp/
```

### Self-hosted instances

To point at a different Oplane server, set the `OPLANE_BASE_URL` environment variable:

```bash
export OPLANE_BASE_URL=https://your-oplane-instance.com
```

## Usage

### Analyze a codebase

```
/oplane:analyze
```

Performs a full security threat model analysis. Optionally focus on a specific area:

```
/oplane:analyze authentication and session management
```

### Analyze a pull request

```
/oplane:analyze-pr
```

Analyzes the current PR changes for security implications. Provide context:

```
/oplane:analyze-pr PR #123 adds OAuth login flow
```

### Security agent

The plugin also provides a `security-agent` subagent that Claude Code can invoke automatically when security analysis is needed.

## Available tools

The plugin provides access to these Oplane MCP tools:

| Tool | Description |
|------|-------------|
| `new_threatmodel` | Create threat models with security requirements |
| `request_implementation_advice` | Get implementation guidance (supports batch) |
| `update_implementation_state` | Record implementation assessments |
| `update_requirement_severity` | Adjust severity with motivation |
| `my_recent_threatmodels` | List existing threat models |
| `add_threatmodel_comment` | Add context to refine threat models |

## License

Proprietary. See [Oplane](https://www.oplane.io) for terms.
