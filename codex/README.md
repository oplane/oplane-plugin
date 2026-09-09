# Oplane for Codex

Codex has a plugin + marketplace model. Install the Oplane plugin (skills + MCP server) from the
Oplane marketplace, then authenticate over OAuth — no tokens to copy.

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
