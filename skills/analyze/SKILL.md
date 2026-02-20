---
description: Analyze the current project for security threats and generate a threat model with implementation assessments
---

Perform a security threat model analysis on this codebase.

$ARGUMENTS

**IMPORTANT: You MUST complete ALL steps below.** Creating the threat model (step 4) is only the midpoint — you MUST also get implementation advice, assess every requirement against the actual code, and call `update_implementation_state` for each one. Do NOT stop after `new_threatmodel` returns.

## Instructions

1. **Verify Oplane MCP tools are available (MANDATORY)** — Call `my_recent_threatmodels` to verify the Oplane MCP connection is working. **If this call fails or the tools are not available, STOP IMMEDIATELY.** Do not proceed with any analysis. Report the error and tell the user to run `/mcp` to check server status and authenticate. Never perform analysis without working Oplane tools — local-only results cannot be persisted and are not acceptable. If the call succeeds, also check for existing threat models to avoid duplicates.

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

7. **Summarize** — Report the number of threat models, total requirements, implementation state breakdown, and key findings.
