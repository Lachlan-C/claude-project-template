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

- **No plaintext passwords or API keys.** Use appropriate hashing (bcrypt, argon2, etc.).
- **No string interpolation in SQL.** All queries use parameterized bindings.
- **No logging secrets** (passwords, API keys, tokens).
- **No committing secrets to git.** `.env` must be in `.gitignore`.
- **No skipping auth verification on protected routes.**
- **No skipping CSRF tokens on form submissions** (if applicable).
- **No cross-tenant data access.** Every query scoped to authenticated user's context.
- **No business logic in route handlers.** Routes only parse input, call services, return responses.
- **No deploying containers as root.**
- **No baking secrets into Docker images.**

## Always Do (After Every Task)

- Run the quality gate (see `CLAUDE.md` for the exact command).
- Use parameterized queries for all database access.
- Scope all data queries to the authenticated user's context.
- Log every mutation to the audit trail.
- Validate all user input at the service layer boundary.
- Handle all errors explicitly — never swallow exceptions.
- Keep secrets in `.env` (gitignored) and reference via environment variables.
- Write unit tests for pure business logic.

## Ask First (Needs Human Review)

- Adding new database tables or columns (check `docs/schema.md` first).
- Adding or modifying API endpoints (check `docs/api.md` first).
- Changing authentication or authorization logic.
- Modifying the middleware stack order (security-critical).
- Adding new dependencies.
- Modifying Docker or deployment configuration.

## Language & Stack Constraints

<!-- TODO: fill in after project is initialized -->
<!-- e.g. Always use Python 3.12+. No other backend language. -->
<!-- e.g. No external JS frameworks. HTMX + server-rendered HTML only. -->

## Architecture

- 3-layer separation: Routes (presentation) → Services (business logic) → Data (storage).
- No business logic in route handlers. Routes only parse input, call services, return responses.
- Atomic operations: related writes must happen in a single database transaction.
- Audit trail: every mutation logged with actor, action, resource, IP, timestamp.

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
4. No secrets in staged files
5. All new SQL uses parameterized bindings
