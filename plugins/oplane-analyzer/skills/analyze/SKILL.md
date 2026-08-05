---
name: analyze
description: Analyze the current project for security threats and generate a threat model with implementation assessments
---

Perform a security threat model analysis on this codebase.

$ARGUMENTS

**IMPORTANT: You MUST complete ALL steps below.** Creating the threat model (step 4) is only the midpoint — you MUST also get implementation advice, assess every requirement against the actual code, and call `update_implementation_state` for each one. Do NOT stop after `new_threatmodel` returns.

## Instructions

1. **Verify Oplane MCP tools are available (MANDATORY)** — Call `my_recent_threatmodels` to verify the Oplane MCP connection is working. **If this call fails or the tools are not available, STOP IMMEDIATELY.** Do not proceed with any analysis. Report the error and tell the user to check the Oplane MCP server status: in Claude Code run `/mcp`, in Cursor open Settings > MCP. Never perform analysis without working Oplane tools — local-only results cannot be persisted and are not acceptable. If the call succeeds, also check for your existing threat models to avoid duplicates.

2. **Explore the project** — Understand what the application does, its tech stack, architecture, and security-critical components. Read the README, configuration files, route definitions, and key source files.

3. **Identify scopes** — Find threat modeling scopes:
   - **Use-case scopes**: Real user workflows (e.g., "User checkout with payment", "Admin user management")
   - **Integration scopes**: External service interactions (e.g., "Stripe payment processing", "S3 file uploads")
   - **Security scopes**: Cross-cutting concerns (e.g., "Session management", "API authentication")

4. **Create threat models** — Call `new_threatmodel` for each scope with a descriptive title and detailed change_description covering data flows, external services, and what could go wrong.

5. **Assess every requirement** — For each generated requirement:
   a. Call `request_implementation_advice` in batches of **3–5 IDs** (not all at once — large batches exceed output limits)
   b. Search the codebase to verify implementation
   c. Call `update_implementation_state` with your assessment and explanation

6. **Adjust severity** — If any requirement's severity doesn't match the actual risk context, use `update_requirement_severity` with a motivation note.

7. **Verify completeness before reporting (MANDATORY)** — Call `get_threatmodel` and check that **every** requirement has a non-null `implementation_state`. Do not report from your own memory of which ones you assessed; read the persisted model back and count. If any are unassessed, assess them now and re-check.

   **Regenerating a model CLEARS every existing assessment.** Passing `threatmodel_id` to `new_threatmodel` re-runs generation, and requirements come back with `implementation_state: null` — including ones you assessed minutes earlier and ones whose code did not change. "Their verdicts still stand" is false: the verdicts are gone from the record even when the reasoning is still valid. After ANY regeneration, re-assess the full set, not just the requirements whose code changed. Budget for this before choosing to regenerate — for a small delta, a new model may cost less than re-assessing everything on the old one.

   **PARTIALLY_IMPLEMENTED is not IMPLEMENTED. Neither is NOT_IMPLEMENTED.** A model carrying either is NOT a passing result — never describe the run as green, clean, complete, or ready to merge while one exists, and never let the count of IMPLEMENTED requirements stand in for the outcome. Each non-IMPLEMENTED requirement has exactly two honest resolutions: (a) close the gap in this change so it becomes IMPLEMENTED, or (b) put the decision to the user and, if they accept it, record their decision as `ACCEPTED_RISK` / `OUT_OF_SCOPE` / `NOT_APPLICABLE` with the reason. Leaving it PARTIAL is neither — it is an open gap nobody has agreed to own. Lead the summary with the non-IMPLEMENTED ones and what each still needs; do not bury them under the passing count.

8. **Summarize** — Report the number of threat models, total requirements, implementation state breakdown, and key findings. State the counts from the `get_threatmodel` read in step 7, not from recollection.
