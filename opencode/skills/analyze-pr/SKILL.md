---
name: analyze-pr
description: Analyze a pull request for security implications and assess whether security requirements are addressed
---

Analyze this pull request for security implications and create a threat model.

**IMPORTANT: You MUST complete ALL steps below.** Creating the threat model (step 3) is only the midpoint — you MUST also get implementation advice, assess every requirement against the actual code, and call `update_implementation_state` for each one. Do NOT stop after `new_threat_model` returns.

## Instructions

1. **Verify Oplane MCP tools are available (MANDATORY)** — Call `my_recent_threat_models` to verify the Oplane MCP connection is working. **If this call fails or the tools are not available, STOP IMMEDIATELY.** Do not proceed with any analysis. Report the error and tell the user to check the Oplane MCP server status: check your opencode MCP configuration (see https://opencode.ai/docs/mcp-servers/). Never perform analysis without working Oplane tools — local-only results cannot be persisted and are not acceptable.

2. **Understand the PR changes** — Review the PR title, description, and linked issues. Examine the diff to understand what changed. Use Read/Grep for additional context from the codebase.

3. **Create a threat model** — Call `new_threat_model` with:
   - Title: a descriptive name for the PR changes
   - change_description: comprehensive description of what changed, why, and what's affected
   - Include PR metadata if available (pull_request_url, git_repositories, etc.)

4. **Get implementation advice** — Call `request_implementation_advice` in batches of **3–5 IDs at a time** (not all at once — large batches exceed output limits).

5. **Assess each requirement** — For every requirement:
   - Check if the PR changes address it
   - Call `update_implementation_state` with one of:
     - IMPLEMENTED: the essence of the requirement is addressed correctly (good enough). Only minor gaps remain, e.g. additional-case FAILs: name them, don't downgrade.
     - PARTIALLY_IMPLEMENTED: significant parts are addressed, but serious holes remain, i.e. a primary case FAILs.
     - NOT_IMPLEMENTED: the requirement is not addressed; nothing significant is in place.
     - OUT_OF_SCOPE: handled elsewhere (e.g. infrastructure, API gateway).
     - ACCEPTED_RISK: risk acknowledged, not mitigated.
     - NOT_APPLICABLE: does not apply to this context.
     ACCEPTED_RISK only when explicitly accepted in the PR description/comments.
   - Reference specific files and line changes in your description

6. **Adjust severity** — Use `update_security_requirement_severity` if the default doesn't match the actual risk for this PR's context.

7. **Verify completeness before reporting (MANDATORY)** — Call `get_threat_model` and confirm **every** requirement has a non-null `implementation_state`. Read the persisted model back and count; do not report from your own memory of what you assessed. Assess any that are missing, then re-check.

   **Regenerating a model CLEARS every existing assessment.** Passing `threat_model_id` to `new_threat_model` (e.g. after pushing review fixes to the PR) re-runs generation and returns every requirement with `implementation_state: null` — including ones already assessed and ones whose code did not change. After ANY regeneration, re-assess the full set, not just what changed.

   **PARTIALLY_IMPLEMENTED is not IMPLEMENTED. Neither is NOT_IMPLEMENTED.** A PR whose model carries either is NOT green and NOT ready to merge — never report it as such, and never let the IMPLEMENTED count stand in for the outcome. Each non-IMPLEMENTED requirement has two honest resolutions: fix it in this PR so it becomes IMPLEMENTED, or put the decision to the user and record their answer as `ACCEPTED_RISK` / `OUT_OF_SCOPE` / `NOT_APPLICABLE` with the reason. Leaving it PARTIAL is an open gap nobody has agreed to own. Lead the summary with those and what each still needs.

8. **Summarize** — Report the number of requirements, implementation state breakdown, and key findings with recommendations for the PR author. Use the counts from step 7's read, not recollection.
