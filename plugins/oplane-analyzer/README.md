# Oplane Plugin

AI-powered security analysis for your codebase — threat modeling, implementation assessment, and PR security review.

Works with **Claude Code**, **Cursor**, **GitHub Copilot CLI**, **opencode**, and **Codex**.

## What it does

This plugin connects your IDE to [Oplane](https://gravity.oplane.io), giving you:

- **Codebase analysis** — Identify security threats across your entire project
- **PR review** — Analyze pull requests for security implications
- **Implementation assessment** — Check if security requirements are properly implemented
- **Severity management** — Adjust requirement severity based on your risk context

Results are saved to Oplane and visible in the [Gravity web interface](https://gravity.oplane.io).

## Prerequisites

- [Claude Code](https://code.claude.com), [Cursor](https://cursor.com), [GitHub Copilot CLI](https://docs.github.com/en/copilot/how-tos/copilot-cli), [opencode](https://opencode.ai), or [Codex](https://developers.openai.com/codex)
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

### Codex

From your terminal:

```bash
codex plugin marketplace add oplane/oplane-plugin
```

Then start Codex, run `/plugins`, select **oplane**, and install it. Restart your Codex session so the bundled skills and MCP server load, then authenticate with `codex mcp login oplane`. See [`codex/README.md`](codex/README.md) for details. Codex has no subagent, so the `security-analyzer` agent is not included — its skills cover the workflow.

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

### Application keys (automation & CI)

For non-interactive use — CI pipelines, integrations, or any automation running without a
person — authenticate with an **Application key** instead of a personal token. An
Application is an org-owned identity with a fixed permission set and workspace scope, so a
pipeline gets exactly the access it needs and nothing more.

An **org admin** creates one in [Gravity](https://gravity.oplane.io) → **Organization
settings → Applications**:

1. **New application** — give it a name.
2. Choose its **permissions** (metadata / contents / organization administration, each at
   read or read & write) and its **workspace scope** (all workspaces in the org, or a
   selected subset).
3. **Generate key** and choose an expiry (1–365 days; default 30). The `oak_v1_…` token is
   shown **once** — copy it now; it cannot be recovered later (regenerate to replace it).

Use the token exactly like a PAT:

```bash
claude mcp add --transport http \
  --header "Authorization: Bearer oak_v1_..." \
  oplane https://gravity.oplane.io/mcp/
```

The same key also authenticates the `/api/v1/*` REST API (send it in the `X-API-Key`
header). It is **not** valid on the GraphQL endpoint. Application keys must be enabled for
your organization — ask your Oplane contact if the **Applications** tab isn't visible.

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
| `new_threat_model` | Create threat models with security requirements |
| `request_implementation_advice` | Get implementation guidance (supports batch) |
| `update_implementation_state` | Record implementation assessments |
| `update_security_requirement_severity` | Adjust severity with motivation |
| `my_recent_threat_models` | List your own recent threat models |
| `add_threat_model_comment` | Add context to refine threat models |

## License

Proprietary. See [Oplane](https://www.oplane.io) for terms.
