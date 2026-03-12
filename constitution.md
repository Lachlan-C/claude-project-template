# Constitution - Immutable Rules

> **Related files:** [CLAUDE.md](CLAUDE.md) (routing protocol + skill index) | [docs/design.md](docs/design.md) (system design) | [docs/adr/](docs/adr/) (why) | [docs/spec.md](docs/spec.md) (what & why)

These rules are non-negotiable. They apply to every phase, every task, every commit.

---

## Three-Tier Boundary System

### Always Do (safe actions, no approval needed)

- Run the quality gate after every task
- Validate all input at system boundaries (user input, external data, file I/O)
- Handle all errors explicitly — never swallow exceptions or panic on external data
- Keep secrets out of source code and version control
- Write unit tests for pure logic

### Ask First (high-impact, needs human review)

- Adding new dependencies
- Changing the project's public interface or API surface
- Any change to this constitution file

<!-- OPTIONAL: Add these if your project has a database:
- Adding new database tables or columns (check schema.md first)
-->

<!-- OPTIONAL: Add these if your project has a network API:
- Adding or modifying API endpoints (check api.md first)
- Changing authentication or authorization logic
- Modifying the middleware stack order (security-critical)
-->

<!-- OPTIONAL: Add these if your project is deployed:
- Modifying Docker or deployment configuration
-->

### Never Do (hard stops)

- Store plaintext passwords or API keys
- Log secrets (passwords, API keys, tokens)
- Commit secrets to git

<!-- OPTIONAL: Add these if your project uses SQL:
- Use string interpolation in SQL queries (use parameterized bindings)
-->

<!-- OPTIONAL: Add these if your project has auth/multi-tenancy:
- Skip auth verification on protected routes
- Allow cross-tenant data access
-->

<!-- OPTIONAL: Add these if your project is a web app:
- Skip CSRF tokens on form submissions
- Put business logic in route handlers
-->

<!-- OPTIONAL: Add these if your project is containerized:
- Deploy containers as root
- Bake secrets into Docker images
-->

---

## Language & Stack

<!-- TODO: fill in your language and stack constraints -->
<!-- e.g. Always use Python 3.12+. No other backend language. -->
<!-- e.g. Always use Go 1.22+. -->
<!-- e.g. Always use Rust stable. No nightly features. -->
<!-- e.g. Always use C17 standard. -->

## Money & Arithmetic (if applicable)

<!-- TODO: fill in if project handles money -->
<!-- e.g. NEVER use floating point for money. Store as integer cents/micro-units. -->

## Security - Hard Requirements

- NEVER store plaintext passwords or secrets. Use appropriate hashing.
- NEVER log secrets (passwords, API keys, tokens).
- NEVER commit secrets to git.
- Input from external sources must be validated before use.

<!-- OPTIONAL: Add these if your project uses SQL:
- NEVER use string interpolation in SQL queries. Use parameterized bindings.
-->

<!-- OPTIONAL: Add these if your project has network-facing auth:
- Auth must be verified on every protected route via middleware.
- Tenant/org isolation: every data query must be scoped to the authenticated user's context.
- API key verification must use constant-time comparison.
- CSRF tokens required on ALL form submissions (if applicable).
-->

## Architecture

<!-- TODO: fill in your architecture rules. Examples by project type:

   Web app:        3-layer separation: Routes -> Services -> Data
   CLI tool:       Separation between CLI parsing, core logic, and I/O
   Library:        Clean public API surface, internal implementation hidden
   Compiler:       Pipeline stages: lexer -> parser -> analyzer -> codegen
   Game:           Game loop, state management, rendering separated
-->

## Design Principles

### Anti-Over-Engineering (Read This First)
Every code change must pass this filter. When in doubt, choose the simpler option.
- **After writing code, review it against YAGNI + KISS + DRY.** Remove anything that doesn't survive.
- Don't add error handling, fallbacks, or validation for scenarios that can't happen. Trust internal code and framework guarantees. Only validate at system boundaries.
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
- Shared logic lives in a common module, not duplicated across callers.
- DRY applies to knowledge, not just code. A business rule must have exactly one authoritative implementation.
- **DRY does not override KISS.** Don't create a complex abstraction just to avoid two similar lines.

### Single Responsibility
- Each function does one thing and does it completely.
- Each module owns one domain. Cross-domain logic lives in a dedicated orchestration function.
- Each file has a clear, singular purpose. If a file grows beyond ~300 lines, consider splitting by responsibility.

### Parse, Don't Validate
- Convert unstructured input into typed data at the system boundary. Once parsed, the type guarantees validity.
- Use domain types for domain concepts (e.g. `UserId`, `Email`). The constructor validates; downstream code trusts the type.
- Make illegal states unrepresentable through your type system.

### Fail Fast
- Validate inputs and preconditions at the function entry point. Return errors immediately.
- Prefer returning errors over logging and continuing with default/fallback values. Silent fallbacks hide bugs.
- On startup: fail immediately if required config is missing or invalid.

### Principle of Least Surprise
- Functions do what their name says, nothing more.
- Side effects must be visible: if a function writes to disk, sends a network request, or mutates shared state, its name or signature should make that obvious.

### Convention over Configuration
- Consistent file structure — follow the existing layout.
- Consistent naming conventions — follow language idioms.
- Consistent error handling — all errors flow through a single error type.
- New code follows patterns already established in the codebase.

---

## Testing

### When to Write Tests

- **Every new module/function with logic** gets at least one unit test.
- **Every bug fix** gets a regression test that fails before the fix and passes after.
- **Pure logic** must have exhaustive unit tests covering edge cases.

<!-- OPTIONAL: For projects with network interfaces:
- **Every new route/endpoint** gets at least one integration test.
-->

### Unit Tests

- Test pure functions in isolation — no I/O, no network, no filesystem (where possible).
- Name pattern: `test_<function>_<scenario>` (e.g. `test_calculate_fee_minimum_amount`).
- Cover: happy path, boundary values, and error cases.

### Integration Tests

- Test the system through its public interface (CLI, API, library API).
- Each test gets isolated state — tests must not share mutable state.
- Test full input/output cycles including error cases.

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

## Packaging & Deployment (if applicable)

<!-- OPTIONAL: Uncomment if your project is containerized/deployed:
- Container runs as non-root user.
- No shell in production container.
- No secrets baked into Docker images.
- Read-only filesystem in production (only data directories writable).
-->

---

## Pre-Commit Checklist

Before every commit, verify:

1. Formatter passes
2. Linter passes with zero warnings
3. All tests pass
4. Project builds and runs without errors
5. No secrets in staged files
