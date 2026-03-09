---
name: quality-gate
description: Run the full quality gate as defined in CLAUDE.md and constitution.md
---

Run the project quality gate. Execute the commands defined in CLAUDE.md's `## Executable Commands` section in sequence, stopping on the first failure.

Typical patterns by language:
- **Rust:** `cargo check && cargo fmt -- --check && cargo clippy -- -D warnings && cargo test`
- **Node:** `npm run lint && npm test`
- **Python:** `ruff check . && pytest`

If all steps pass, report success. If any step fails, report which step failed and show the error output.

This is the pre-commit checklist from constitution.md. Run this after every task.
