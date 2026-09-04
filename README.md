# Oplane Plugin

AI-powered security analysis for your codebase — threat modeling, implementation assessment, and PR security review.

Works with **Claude Code**, **Cursor**, **GitHub Copilot CLI**, and **opencode**.

## What it does

This plugin connects your IDE to [Oplane](https://gravity.oplane.io), giving you:

- **Codebase analysis** — Identify security threats across your entire project
- **PR review** — Analyze pull requests for security implications
- **Implementation assessment** — Check if security requirements are properly implemented
- **Severity management** — Adjust requirement severity based on your risk context

Results are saved to Oplane and visible in the [Gravity web interface](https://gravity.oplane.io).

## Prerequisites

- [Claude Code](https://code.claude.com), [Cursor](https://cursor.com), [GitHub Copilot CLI](https://docs.github.com/en/copilot/how-tos/copilot-cli), or [opencode](https://opencode.ai)
- An [Oplane account](https://gravity.oplane.io)

## Installation

### Claude Code

From inside Claude Code:

```bash
/plugin marketplace add oplane/oplane-plugin
/plugin install oplane@oplane-plugins
```

### Cursor

Install from the [Cursor Marketplace](https://cursor.com/marketplace) (when available), or load via Cursor Settings > Plugins and add the repository URL.

### GitHub Copilot CLI

```bash
copilot plugin install /path/to/oplane-plugin
```

Verify with `copilot plugin list` (or `/plugin list` in an interactive session). Re-run `copilot plugin install` after editing the plugin.

### opencode

opencode has no plugin/marketplace mechanism — it loads skills from a directory and MCP servers from its config file, so installation is manual. See [`opencode/README.md`](opencode/README.md) for the copy-paste `curl` commands to install the skills into `~/.config/opencode/skills/` and the MCP config to add.

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

The plugin also provides a `security-analyzer` subagent that Claude Code, Copilot CLI, and opencode can invoke automatically when security analysis is needed.

## Recommended setup: CLAUDE.md / AGENTS.md

The single highest-impact step is making threat modeling a standing instruction your agent
reads every session, so security-relevant changes are modeled before each commit or PR,
not just reviewed after a PR opens. Add this to your project's `CLAUDE.md` (and/or
`AGENTS.md`):

```
For changes that could affect security, you MUST threat-model the change using Oplane MCP
before committing. Threat-model the actual diff (e.g. the PR threat model), not a written
summary of it - a model built from your own description only re-tests risks you already
considered. Explicitly consider untrusted-input-inbound (log/audit/template/SQL injection
from external data), not only outward data leakage.
```

## Available tools

The plugin provides access to these Oplane MCP tools:

| Tool | Description |
|------|-------------|
| `new_threatmodel` | Create threat models with security requirements |
| `request_implementation_advice` | Get implementation guidance (supports batch) |
| `update_implementation_state` | Record implementation assessments |
| `update_requirement_severity` | Adjust severity with motivation |
| `my_recent_threatmodels` | List your own recent threat models |
| `add_threatmodel_comment` | Add context to refine threat models |

## License

Proprietary. See [Oplane](https://www.oplane.io) for terms.
