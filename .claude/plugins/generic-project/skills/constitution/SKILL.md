---
name: constitution
description: >
  This skill should be used when the user asks to "write code", "implement a feature",
  "create a function", "add an endpoint", "modify code", "before coding", "code rules",
  "project rules", "what are the rules", or begins any implementation task. It provides
  the immutable project rules that govern all code in this repository.
---

# Constitution — Immutable Rules

These rules are non-negotiable. They apply to every phase, every task, every commit. The canonical source is `constitution.md` in the project root.

## Never Do (Hard Stops)

Violating any of these is an immediate rollback. No exceptions:

- **No plaintext passwords or API keys.** Use appropriate hashing.
- **No logging secrets** (passwords, API keys, tokens).
- **No committing secrets to git.**
- **No ignoring input validation** at system boundaries.

Check `constitution.md` for project-specific hard stops (SQL injection rules, auth rules, deployment rules, etc. — these vary by project type).

## Always Do (After Every Task)

- Run the quality gate (see `CLAUDE.md` for the exact command).
- Validate all input at system boundaries.
- Handle all errors explicitly — never swallow exceptions.
- Keep secrets out of source code and version control.
- Write unit tests for pure logic.

Check `constitution.md` for project-specific "always do" rules.

## Ask First (Needs Human Review)

- Adding new dependencies.
- Changing the project's public interface or API surface.
- Any change to the constitution file.

Check `constitution.md` for project-specific "ask first" rules (database changes, endpoint changes, auth changes, etc.).

## Language & Stack Constraints

See `constitution.md` — fill in after project is initialized.

## Architecture

See `constitution.md` — architecture rules vary by project type.

## Design Principles

- **KISS**: Prefer the simplest solution that works. No premature abstractions.
- **YAGNI**: Don't build for hypothetical future requirements.
- **DRY**: If the same logic exists in 3+ places, extract it. Two occurrences are fine.
- **Single Responsibility**: Each function does one thing. Each module owns one domain.
- **Parse, Don't Validate**: Convert unstructured input into typed data at the system boundary.
- **Fail Fast**: Validate inputs at entry points. Return errors immediately.

## Code Quality Gate

Before every commit, verify:

1. Formatter passes
2. Linter passes with zero warnings
3. All tests pass
4. Project builds and runs without errors
5. No secrets in staged files
