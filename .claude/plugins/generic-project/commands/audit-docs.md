---
name: audit-docs
description: |
  Use this command when the user asks to audit documentation, check if docs are accurate or up to date, verify documentation after completing a development phase, detect documentation drift after a refactor, or check that docs follow the correct structure and external standards. Examples: "audit the docs", "are the docs still accurate?", "I finished Phase 2, check the docs", "check if my docs have drifted", "validate the docs against standards".
---

Use the Agent tool to dispatch the `docs-auditor` agent to audit all project documentation. Pass the following prompt to the agent:

> Audit all project documentation. Run Phase 1 (internal doc consistency), Phase 2 (docs vs. code), and Phase 3 (template conformance and external standards) as specified in your instructions. Report the full summary organized by category at the end.

The `docs-auditor` agent owns the full audit logic across all three phases, including the optional docs table, document templates, and external standards checks. Do not duplicate that logic here — delegate entirely to the agent.
