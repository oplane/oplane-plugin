# Oplane for Codex

Codex has a plugin + marketplace model. Install the Oplane plugin (skills + MCP server) from the
Oplane marketplace, then authenticate over OAuth — no tokens to copy.

## What it does

This plugin connects your agent to [Oplane](https://www.oplane.com), giving you:

- **Codebase analysis**: identify security threats across your entire project
- **PR review**: analyze pull requests for security implications
- **Implementation assessment**: check if security requirements are properly implemented
- **Severity management**: adjust requirement severity based on your risk context

Results are saved to Oplane and visible in the [Gravity web interface](https://gravity.oplane.io).

## 1. Add the Oplane marketplace

```bash
codex plugin marketplace add oplane/oplane-plugin
```

## 2. Install the plugin

Start Codex, open the plugins picker, select **oplane**, and install it:

```bash
codex
/plugins
```

Restart your Codex session after installing so the bundled skills and MCP server load.

## 3. Authenticate with Oplane

The Oplane MCP server uses OAuth. Sign in when prompted, or trigger it manually:

```bash
codex mcp login oplane
```

A browser opens where you log in; tokens are issued and refreshed automatically.

## 4. Use it

Ask Codex to run a security analysis (e.g. "analyze this project for security threats") and it
loads the `analyze` skill; point it at a pull request for `analyze-pr`. Codex discovers skills by
their description.

## MCP server only (without the plugin)

If you only want the Oplane tools without the bundled skills:

```bash
codex mcp add oplane --url https://gravity.oplane.io/mcp/
```

Codex detects that Oplane uses OAuth and opens a browser to sign in when you add the server — no
tokens needed. If you aren't prompted, authenticate manually with `codex mcp login oplane`.

For a self-hosted Oplane instance, use your server's `/mcp/` endpoint (and edit the URL in the
plugin's `.mcp.json` if you installed via the marketplace).

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

The plugin connects your agent to the Oplane MCP server, which provides the tools the skills
use (create a threat model, get implementation advice, record assessments, and more). For the
full list, see the [MCP tool reference](https://www.oplane.com/docs/reference/mcp-tools).

## What the plugin sends

The plugin connects your agent to Oplane's MCP server at `https://gravity.oplane.io/mcp/`.
When you run an analysis, your agent sends Oplane:

- titles and descriptions of the code or change it is threat modeling, which can include
  short code excerpts
- git details such as the repository URL, branch, commit SHA and pull request URL
- the assessment it records for each security requirement, with file and line references

The plugin sends nothing to any other service. It has no hooks or scripts that run on your
machine: the skills are instructions your agent follows. Oplane stores the
results in your account, as described in the
[privacy policy](https://www.oplane.com/legal/privacy-policy).

## Support

See the [Oplane agent plugins documentation](https://www.oplane.com/docs/reference/agent-plugins) or email [support@oplane.io](mailto:support@oplane.io).

## License

Apache License 2.0. See [LICENSE](LICENSE).
