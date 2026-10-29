# Oplane for opencode

opencode has no plugin/marketplace mechanism — it loads **skills** and **subagents** from
directories and **MCP servers** from its config file. So installation is three manual
steps: copy the skill files, copy the subagent, then add the MCP server.

## What it does

This plugin connects your agent to [Oplane](https://www.oplane.com), giving you:

- **Codebase analysis**: identify security threats across your entire project
- **PR review**: analyze pull requests for security implications
- **Implementation assessment**: check if security requirements are properly implemented
- **Severity management**: adjust requirement severity based on your risk context

Results are saved to Oplane and visible in the [Gravity web interface](https://gravity.oplane.io).

## 1. Install the skills

opencode reads skills from `~/.config/opencode/skills/<name>/SKILL.md`. Pull each skill
file directly into place:

```bash
mkdir -p ~/.config/opencode/skills/analyze ~/.config/opencode/skills/analyze-pr

curl -fsSL -o ~/.config/opencode/skills/analyze/SKILL.md \
  https://raw.githubusercontent.com/oplane/oplane-plugin/main/opencode/skills/analyze/SKILL.md

curl -fsSL -o ~/.config/opencode/skills/analyze-pr/SKILL.md \
  https://raw.githubusercontent.com/oplane/oplane-plugin/main/opencode/skills/analyze-pr/SKILL.md
```

To install per-project instead of globally, replace `~/.config/opencode/skills` with
`.opencode/skills` in your project root.

## 2. Install the security-analyzer subagent

opencode reads subagents from `~/.config/opencode/agents/<name>.md`. The `security-analyzer`
subagent runs the full threat-modeling workflow and is invoked automatically by your primary
agent (or manually with `@security-analyzer`):

```bash
mkdir -p ~/.config/opencode/agents

curl -fsSL -o ~/.config/opencode/agents/security-analyzer.md \
  https://raw.githubusercontent.com/oplane/oplane-plugin/main/opencode/agents/security-analyzer.md
```

As with skills, use `.opencode/agents` in your project root for a per-project install.

## 3. Configure the Oplane MCP server

Add the Oplane MCP server to your opencode config (`~/.config/opencode/opencode.json`, or
`opencode.json` in your project root). Merge this `mcp` block into your existing config:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "oplane": {
      "type": "remote",
      "url": "https://gravity.oplane.io/mcp/",
      "enabled": true
    }
  }
}
```

For a self-hosted Oplane instance, change the `url` to your server's `/mcp/` endpoint.

If your instance requires a Personal Access Token, add a headers block:

```json
"headers": { "Authorization": "Bearer YOUR_PAT_TOKEN" }
```

See the opencode MCP docs for details: https://opencode.ai/docs/mcp-servers/

## 4. Use it

Ask opencode to run a security analysis (e.g. "analyze this project for security threats")
and it will load the `analyze` skill. For a pull request, point it at the PR and it will
load `analyze-pr`. opencode discovers skills by their description and invokes them via its
`skill` tool — there are no slash commands to remember. For a deeper, self-directed review
it can hand off to the `security-analyzer` subagent (or you can call it with
`@security-analyzer`).

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
machine: the skills and the agent are instructions your agent follows. Oplane stores the
results in your account, as described in the
[privacy policy](https://www.oplane.com/legal/privacy-policy).

## Support

See the [Oplane agent plugins documentation](https://www.oplane.com/docs/reference/agent-plugins) or email [support@oplane.io](mailto:support@oplane.io).

## License

Apache License 2.0. See [LICENSE](LICENSE).
