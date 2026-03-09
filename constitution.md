# Constitution - Immutable Rules

> **Related files:** [CLAUDE.md](CLAUDE.md) (routing protocol + skill index) | [docs/design.md](docs/design.md) (system design) | [docs/adr/](docs/adr/) (why) | [docs/spec.md](docs/spec.md) (what & why) | [docs/schema.md](docs/schema.md) (tables) | [docs/api.md](docs/api.md) (endpoints)

These rules are non-negotiable. They apply to every phase, every task, every commit.

---

## Three-Tier Boundary System

### Always Do (safe actions, no approval needed)

- Run the quality gate after every task
- Use parameterized queries for all database access
- Scope all data queries to the authenticated user's context
- Log every mutation to the audit trail
- Validate all user input at the service layer boundary
- Handle all errors explicitly — never swallow exceptions or panic on external data
- Keep secrets in `.env` (gitignored) and reference via environment variables
- Write unit tests for pure business logic

### Ask First (high-impact, needs human review)

- Adding new database tables or columns (check [schema.md](docs/schema.md) first)
- Adding or modifying API endpoints (check [api.md](docs/api.md) first)
- Changing authentication or authorization logic
- Modifying the middleware stack order (security-critical)
- Adding new dependencies
- Modifying Docker or deployment configuration
- Any change to this constitution file

### Never Do (hard stops)

- Store plaintext passwords or API keys
- Use string interpolation in SQL queries
- Log secrets (passwords, API keys, tokens)
- Commit secrets to git
- Skip auth verification on protected routes
- Skip CSRF tokens on form submissions (if applicable)
- Allow cross-tenant data access
- Put business logic in route handlers
- Deploy containers as root
- Bake secrets into Docker images

---

## Language & Stack

<!-- TODO: fill in your language and stack constraints -->
<!-- e.g. Always use Python 3.12+. No other backend language. -->
<!-- e.g. No external JS frameworks. HTMX + server-rendered HTML only. -->

## Money & Arithmetic (if applicable)

<!-- TODO: fill in if project handles money -->
<!-- e.g. NEVER use floating point for money. Store as integer cents/micro-units. -->

## Security - Hard Requirements

- NEVER store plaintext passwords or secrets. Use appropriate hashing (bcrypt, argon2, etc.).
- NEVER use string interpolation in SQL queries. Use parameterized bindings.
- NEVER log secrets (passwords, API keys, tokens).
- NEVER commit secrets to git. `.env` must be in `.gitignore`.
- Auth must be verified on every protected route via middleware.
- Tenant/org isolation: every data query must be scoped to the authenticated user's context.
- API key verification must use constant-time comparison.
- CSRF tokens required on ALL form submissions (if applicable).

## Architecture

<!-- TODO: fill in your architecture rules -->
<!-- e.g. 3-layer separation: Routes -> Services -> Data -->
- No business logic in route handlers. Routes only parse input, call services, return responses.
- Atomic operations: related writes must happen in a single database transaction.
- Audit trail: every mutation must be logged with actor, action, resource, IP, timestamp.

## Design Principles

### Anti-Over-Engineering (Read This First)
Every code change must pass this filter. When in doubt, choose the simpler option.
- **After writing code, review it against YAGNI + KISS + DRY.** Remove anything that doesn't survive.
- Don't add error handling, fallbacks, or validation for scenarios that can't happen. Trust internal code and framework guarantees. Only validate at system boundaries (user input, external APIs).
- Don't create helpers, utilities, or abstractions for one-time operations. Three similar lines of code is better than a premature abstraction.
- Don't add feature flags, backwards-compatibility shims, or configuration options unless explicitly requested.
- Don't add comments, docstrings, or type annotations to code you didn't change. Only add comments where the logic isn't self-evident.
- Don't refactor surrounding code when fixing a bug. Fix the bug, nothing more.
- If a function is only called once, it probably doesn't need to be extracted — unless it significantly improves readability.

### YAGNI (You Aren't Gonna Need It)
- Do not build for hypothetical future requirements. Build for what is needed now.
- No speculative generality: don't add parameters, config options, or extension points "just in case."
- No unused abstractions: if something exists without a current consumer, delete it.
- No premature optimization: profile before optimizing. Readable code first, fast code when measured.

### KISS (Keep It Simple, Stupid)
- Prefer the simplest solution that works. No premature abstractions.
- No dynamic dispatch / polymorphism unless there is a concrete second implementation.
- No generics/templates unless used with 2+ concrete types.
- One error type with appropriate conversions — no per-module error hierarchies.

### DRY (Don't Repeat Yourself)
- If the same logic exists in 3+ places, extract it. Two occurrences are fine — wait for the third.
- Shared logic lives in the service layer or a utils module, never duplicated across route handlers.
- DRY applies to knowledge, not just code. A business rule must have exactly one authoritative implementation.
- **DRY does not override KISS.** Don't create a complex abstraction just to avoid two similar lines.

### Single Responsibility
- Each function does one thing and does it completely.
- Each module owns one domain. Cross-domain logic lives in a dedicated orchestration function.
- Each file has a clear, singular purpose. If a file grows beyond ~300 lines, consider splitting by responsibility.
- Route handlers have exactly one job: parse input, call service, return response. No business logic.

### Parse, Don't Validate
- Convert unstructured input into typed data at the system boundary. Once parsed, the type guarantees validity.
- Use domain types for domain concepts (e.g. `UserId`, `Email`). The constructor validates; downstream code trusts the type.
- Make illegal states unrepresentable through your type system.

### Fail Fast
- Validate inputs and preconditions at the function entry point. Return errors immediately.
- Prefer returning errors over logging and continuing with default/fallback values. Silent fallbacks hide bugs.
- On startup: fail immediately if required env vars are missing, DB is unreachable, or config is invalid.

### Principle of Least Surprise
- Functions do what their name says, nothing more.
- API endpoints return standard HTTP status codes. No surprises.
- Side effects must be visible: if a function writes to the DB or calls an external service, its name or signature should make that obvious.

### Convention over Configuration
- Consistent file structure — follow the existing layout.
- Consistent naming conventions — follow language idioms.
- Consistent error handling — all errors flow through a single error type.
- New code follows patterns already established in the codebase.

---

## Testing

### When to Write Tests

- **Every new service-layer function** gets at least one unit test.
- **Every new route/endpoint** gets at least one integration test.
- **Every bug fix** gets a regression test that fails before the fix and passes after.
- **Pure business logic** must have exhaustive unit tests covering edge cases.

### Unit Tests

- Test pure functions in isolation — no database, no HTTP, no filesystem.
- Name pattern: `test_<function>_<scenario>` (e.g. `test_calculate_fee_minimum_amount`).
- Cover: happy path, boundary values, and error cases.

### Integration Tests

- Use a real HTTP client against a test server instance.
- Each test gets a fresh database — tests must not share state.
- Test full request/response cycles: correct status codes, response bodies, headers, and DB side effects.

### What NOT to Test

- Don't test library/framework internals.
- Don't test trivial constructors unless they contain logic.
- Don't write tests that just restate the implementation.

---

## Code Quality

- Linter must pass with zero warnings.
- Formatter must pass.
- All tests must pass.
- Every task must leave the project in a runnable state.
- No dead code accumulation — fix or remove immediately.

## Docker & Deployment (if applicable)

- Container runs as non-root user.
- No shell in production container.
- No secrets baked into Docker images.
- Read-only filesystem in production (only data directories writable).

---

## Pre-Commit Checklist

Before every commit, verify:

1. Formatter passes
2. Linter passes with zero warnings
3. All tests pass
4. Server starts without errors
5. No secrets in staged files
6. `.env` is not staged
7. No unsafe error handling on user input
8. All new SQL uses parameterized bindings
