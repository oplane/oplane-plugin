# Oplane for opencode

opencode has no plugin/marketplace mechanism — it loads **skills** and **subagents** from
directories and **MCP servers** from its config file. So installation is three manual
steps: copy the skill files, copy the subagent, then add the MCP server.

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
