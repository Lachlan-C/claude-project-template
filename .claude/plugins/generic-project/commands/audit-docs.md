---
name: audit-docs
description: Audit docs as plan of record — consistency, implementation gaps, template conformance, and external standards
---

Dispatch the docs-auditor agent to audit all project documentation in three phases.

Use the Agent tool to run the documentation audit. The agent will:

**Phase 1 — Docs ↔ Docs:** Get the plan internally consistent first. Validate the document dependency chain (spec → design → tasks → schema/api), verify cross-links, detect duplication, and prune stale/ungrounded content.

**Phase 2 — Docs ↔ Code:** Compare the consistent plan against source code. Discrepancies are reported as implementation gaps (code needs to catch up) unless the code deviation is logically justified, in which case the doc is updated and the change is propagated upstream.

**Phase 3 — Docs ↔ Templates + External Standards:** Validate each doc against its required template structure (correct sections, correct ownership boundaries, no misplaced content, no thin/empty sections). Then fetch official documentation for each technology in the project and verify that the docs accurately describe usage, required configuration, security requirements, and version-specific patterns. Gaps are filled where possible; items requiring user decisions are flagged.

This should be run after completing a phase of work, after significant refactors, or when documentation drift is suspected.
