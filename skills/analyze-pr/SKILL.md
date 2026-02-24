---
description: Analyze a pull request for security implications and assess whether security requirements are addressed
---

Analyze this pull request for security implications and create a threat model.

$ARGUMENTS

**IMPORTANT: You MUST complete ALL steps below.** Creating the threat model (step 3) is only the midpoint — you MUST also get implementation advice, assess every requirement against the actual code, and call `update_implementation_state` for each one. Do NOT stop after `new_threatmodel` returns.

## Instructions

1. **Verify Oplane MCP tools are available (MANDATORY)** — Call `my_recent_threatmodels` to verify the Oplane MCP connection is working. **If this call fails or the tools are not available, STOP IMMEDIATELY.** Do not proceed with any analysis. Report the error and tell the user to check the Oplane MCP server status: in Claude Code run `/mcp`, in Cursor open Settings > MCP. Never perform analysis without working Oplane tools — local-only results cannot be persisted and are not acceptable.

2. **Understand the PR changes** — Review the PR title, description, and linked issues. Examine the diff to understand what changed. Use Read/Grep for additional context from the codebase.

3. **Create a threat model** — Call `new_threatmodel` with:
   - Title: a descriptive name for the PR changes
   - change_description: comprehensive description of what changed, why, and what's affected
   - Include PR metadata if available (pull_request_url, git_repositories, etc.)

4. **Get implementation advice** — Call `request_implementation_advice` in batches of **3–5 IDs at a time** (not all at once — large batches exceed output limits).

5. **Assess each requirement** — For every requirement:
   - Check if the PR changes address it
   - Call `update_implementation_state` with one of:
     - **IMPLEMENTED** — Security control is present in the PR changes
     - **PARTIALLY_IMPLEMENTED** — Some aspects addressed, others missing
     - **NOT_IMPLEMENTED** — No evidence in the PR changes
     - **OUT_OF_SCOPE** — Handled elsewhere (infrastructure, separate PR)
     - **ACCEPTED_RISK** — Explicitly accepted in PR description/comments
   - Reference specific files and line changes in your description

6. **Adjust severity** — Use `update_requirement_severity` if the default doesn't match the actual risk for this PR's context.

7. **Summarize** — Report the number of requirements, implementation state breakdown, and key findings with recommendations for the PR author.
