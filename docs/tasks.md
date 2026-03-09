# [Project Name] - Tasks

> **Related files:** [design.md](design.md) (system design & phases) | [adr/](adr/) (why) | [environment.md](environment.md) (env vars, deps, Docker) | [spec.md](spec.md) (acceptance criteria) | [schema.md](schema.md) (tables) | [api.md](api.md) (endpoints) | [../constitution.md](../constitution.md) (immutable rules)
>
> **This doc owns:** Task sequence, parallelism markers, file paths, validation gates. Endpoint contracts live in [api.md](api.md). Schema details live in [schema.md](schema.md). Validation gates reference those docs rather than duplicating their content.

Sequential task IDs. Complete in order unless marked `[parallel]`.
After each task, run the quality gate checks before moving on.

## Quality Gate (run after every task)

```bash
# TODO: replace with your quality gate
# e.g. Rust:   cargo check && cargo fmt && cargo clippy -- -D warnings && cargo test
# e.g. Node:   npm run lint && npm test
# e.g. Python: ruff check . && pytest
```

---

## Phase 1: Project Skeleton & Database

> **Goal:** Compiling/running project with health check endpoint and database migrations applied.
> **Key docs:** [design.md](design.md) for project structure, [schema.md](schema.md) for table schemas.

- [ ] **T001** Initialize project
  - Files: `[Cargo.toml / package.json / pyproject.toml]`, `src/main.[ext]`
- [ ] **T002** Add dependencies — see [environment.md#key-dependencies](environment.md#key-dependencies) for exact versions
- [ ] **T003** Create `.env.example` (template, no secrets) and `.env` (gitignored) `[parallel: T002]`
  - Files: `.env.example`, `.env`, `.gitignore`
  - Ref: [environment.md#environment-variables](environment.md#environment-variables) for all vars
- [ ] **T004** Write config loading — load env vars with validation on startup
  - Files: `src/config.[ext]`
- [ ] **T005** Write database setup — connection pool + migration runner
  - Files: `src/db.[ext]`
- [ ] **T006** Create `migrations/001_initial.sql` — core tables + indexes `[parallel: T005]`
  - Ref: [schema.md](schema.md) for exact schema
- [ ] **T007** Write error types
  - Files: `src/error.[ext]`
- [ ] **T008** Write main entry point — server setup, router, health check endpoint
  - Files: `src/main.[ext]`
  - Ref: [api.md#health-check](api.md#health-check)
- [ ] **T008b** Write integration test for health check endpoint `[parallel: T009]`
  - Test: `GET /health` returns 200 with expected body
- [ ] **T009** Create module scaffolding `[parallel: T008]`
  - Files: `src/middleware/`, `src/routes/`, `src/services/`
- [ ] **T010** Docker Compose: app + any sidecars `[parallel: T009]`
  - Files: `docker-compose.yml`, `.dockerignore`
  - Ref: [environment.md#docker-strategy](environment.md#docker-strategy)

### Validation Gate 1
- [ ] Quality gate passes
- [ ] Server starts, `GET /health` returns 200
- [ ] Health check integration test passes
- [ ] Database migrations applied (all tables exist)
- [ ] `.env` is in `.gitignore`
- [ ] Project structure matches [design.md#project-structure](design.md#project-structure)
- [ ] No `TODO`, stub placeholders, or `unimplemented` markers left in committed code
- [ ] **Spec compliance check** — read [schema.md](schema.md), then verify the migration files match exactly: table names, column names, types, constraints, and indexes. If anything differs, uncheck this gate and fix before proceeding.
- [ ] **Language & stack check** — verify no banned dependencies crept in: check that only the approved languages, frameworks, and databases defined in [constitution.md#language--stack](../constitution.md#language--stack) are present in the dependency manifest. If any violation is found, uncheck this gate and fix before proceeding.
- [ ] **Design principles check** — read [constitution.md#design-principles](../constitution.md#design-principles) and review the code written in this phase: no abstractions built for a single consumer, no speculative config options or extension points, no unnecessary helpers, no comments on trivial code. If any violation is found, uncheck this gate and fix before proceeding.

---

## Phase 2: Auth & Security Middleware

> **Goal:** Working auth flow, security middleware stack.
> **Key docs:** [api.md](api.md) for endpoint contracts, [design.md#security-architecture](design.md#security-architecture) for middleware stack order.

- [ ] **T011** Write auth service/middleware — verify tokens, extract user context
  - Files: `src/middleware/auth.[ext]`
- [ ] **T012** Write security headers middleware `[parallel: T011]`
  - Files: `src/middleware/security_headers.[ext]`
  - Ref: [design.md#security-headers](design.md#security-headers-set-on-every-response)
- [ ] **T013** Add rate limiting `[parallel: T012]`
  - Ref: [api.md#rate-limits](api.md#rate-limits)
- [ ] **T014** Add CSRF protection on all form routes (if applicable) `[parallel: T013]`
- [ ] **T015** Add body size limit middleware `[parallel: T014]`
- [ ] **T016** Write audit log service `[parallel: T011]`
  - Files: `src/services/audit.[ext]`
  - Ref: [schema.md#audit_log](schema.md#audit_log)
- [ ] **T017** Write user/auth routes (login, register)
  - Files: `src/routes/auth.[ext]`
- [ ] **T018** Write login page template `[parallel: T017]`

### Validation Gate 2
- [ ] Quality gate passes
- [ ] All Phase 1 validation gate items still pass (regression guard)
- [ ] Login works: credentials → session/token set
- [ ] Protected routes return 401 without valid auth
- [ ] Security headers present on all responses
- [ ] CSRF token in forms, POST without token returns 403 (if applicable)
- [ ] Rate limiting: excess requests return 429
- [ ] No secrets in logs
- [ ] No `TODO`, stub placeholders, or `unimplemented` markers left in committed code
- [ ] **Spec compliance check** — read [api.md](api.md) for auth endpoints and [design.md#security-architecture](design.md#security-architecture) for middleware stack order and exact security header values, then verify: every endpoint path, method, request/response shape matches the contract; middleware stack order matches the design; all header names and values match. Any discrepancy — uncheck this gate and fix before proceeding.
- [ ] **Design principles check** — read [constitution.md#design-principles](../constitution.md#design-principles) and review the code written in this phase: no unnecessary abstractions or traits for single-use cases, no global mutable state, pure functions used for validation logic, no feature flags or backwards-compatibility shims. If any violation is found, uncheck this gate and fix before proceeding.

---

## Phase 3: [Core Feature(s)]

> **Goal:** [Describe the core domain feature]
> **Key docs:** [spec.md](spec.md) for acceptance criteria, [schema.md](schema.md) for schema, [api.md](api.md) for endpoint contracts.

<!-- TODO: break down your core feature into tasks following the same pattern -->

- [ ] **T019** Write [resource] service — creation, validation, status management
  - Files: `src/services/[resource].[ext]`
- [ ] **T019b** Write unit tests for [resource] service logic `[parallel: T020]`
  - Tests: happy path, edge cases, error cases
- [ ] **T020** Write audit integration in [resource] service `[parallel: T019]`
- [ ] **T021** Add [resource] API endpoints
  - Files: `src/routes/api.[ext]`
  - Ref: [api.md#resource-api](api.md#resource-api)
- [ ] **T021b** Write integration tests for [resource] endpoints `[parallel: T022]`

### Validation Gate 3
- [ ] Quality gate passes
- [ ] All prior validation gate items still pass (regression guard)
- [ ] [Resource] CRUD works end-to-end
- [ ] Unit tests pass for all service logic
- [ ] Integration tests pass for all endpoints
- [ ] Audit log entries created for all mutations
- [ ] Tenant/org isolation enforced
- [ ] No `TODO`, stub placeholders, or `unimplemented` markers left in committed code
- [ ] **Spec compliance check** — read [spec.md](spec.md) acceptance criteria and [api.md](api.md) endpoint contracts for this phase, then verify: each acceptance criterion is satisfied by actual code (not just by a passing test), all endpoint paths/methods/request/response shapes match, all schema fields written to the DB match [schema.md](schema.md). Any deviation — uncheck this gate and fix before proceeding.
- [ ] **Design principles check** — read [constitution.md#design-principles](../constitution.md#design-principles) and review the code written in this phase: pure functions for business logic, domain concepts use newtypes or enums (not raw strings), each function does one thing only, no duplicated business rules across modules. If any violation is found, uncheck this gate and fix before proceeding.

---

## Phase N: Testing, Security Audit & Hardening

> **Goal:** End-to-end tests, cross-cutting security tests, dependency audit.
> **Key docs:** [spec.md#success-criteria](spec.md#success-criteria), [constitution.md](../constitution.md).

- [ ] **TN01** Write end-to-end integration tests — full flow across all layers
- [ ] **TN02** Write cross-cutting security tests `[parallel: TN01]`
  - Covers: auth bypass, CSRF enforcement, rate limiting, tenant isolation
- [ ] **TN03** Run dependency audit — fix any CVE or license issues `[parallel: TN02]`
- [ ] **TN04** Security review: grep for unsafe error handling, plaintext secrets, missing validation `[parallel: TN03]`
- [ ] **TN05** Implement graceful shutdown `[parallel: TN04]`

### Validation Gate N
- [ ] Quality gate passes
- [ ] All prior validation gate items still pass (regression guard)
- [ ] Dependency audit passes (zero HIGH/CRITICAL CVEs)
- [ ] All auth, CSRF, rate limiting, isolation tests pass
- [ ] Graceful shutdown: server drains in-flight requests
- [ ] No unsafe error handling on user input, no plaintext secrets
- [ ] No `TODO`, stub placeholders, or `unimplemented` markers left in committed code
- [ ] **Architecture compliance check** — read [constitution.md](../constitution.md) in full, then verify the entire codebase: error handling follows the constitution rules, no business logic leaks into route handlers, no direct DB access outside the service layer, all security hard requirements are met. This is structural — it catches coupling and layering violations that passing tests won't catch.
- [ ] **Spec compliance check** — read [spec.md#success-criteria](spec.md#success-criteria) and [spec.md#key-properties](spec.md#key-properties) in full, then walk through every item against the actual implementation. This is not a re-run of tests — it is a deliberate line-by-line audit. For each criterion, confirm the code satisfies it (not just that a test passes). Anything unverified or incorrect — uncheck and fix before proceeding.
- [ ] **Testing quality check** — read [constitution.md#testing](../constitution.md#testing) and audit the test suite: every service-layer function has at least one unit test, every endpoint has at least one integration test, unit tests are co-located with source (not only in `tests/`), integration tests each get isolated state (no shared DB or global state between tests), test names follow a consistent `test_<function>_<scenario>` pattern, no tests that just restate the implementation. If any violation is found, uncheck this gate and fix before proceeding.
- [ ] **Dead code check** — no modules, functions, interfaces, or enum variants exist without a current consumer; no dead-code suppression workarounds (e.g. `#[allow(dead_code)]`, `_` prefixes masking real issues); no commented-out code. Confirm the linter reports zero dead code warnings. If any is found, delete it before proceeding.
- [ ] **Design principles check** — read [constitution.md#design-principles](../constitution.md#design-principles) end-to-end and sweep the entire codebase: flag any premature abstractions, any interfaces/traits with a single implementor, any generics used with one concrete type, any helper only called once, any dead variants or speculative extension points, any global mutable state. This is a holistic pass — individual gate checks caught phase-level issues; this catches drift accumulated across the whole codebase. Fix before proceeding.

---

## Phase N+1: Docker & Deployment

> **Goal:** Production-ready Docker image with minimal attack surface.
> **Key docs:** [environment.md#docker-strategy](environment.md#docker-strategy), [constitution.md#docker](../constitution.md#docker--deployment).

- [ ] **TN101** Add release/production build optimizations
- [ ] **TN102** Write `.dockerignore` `[parallel: TN101]`
  - Exclude: build artifacts, `.env`, `.git/`, data directories
- [ ] **TN103** Write multi-stage Dockerfile (build stage + minimal runtime)
  - Ref: [environment.md#docker-strategy](environment.md#docker-strategy)
- [ ] **TN104** Update `docker-compose.yml` for production (volumes, health checks, restart policy) `[parallel: TN103]`
- [ ] **TN105** Build and test Docker image
  - Run: `docker compose build && docker compose up`, verify health check

### Validation Gate N+1
- [ ] `docker compose up` starts successfully
- [ ] All prior validation gate items still pass from inside the container (regression guard)
- [ ] `GET /health` returns 200 from container
- [ ] Docker image is minimal (target < 50MB)
- [ ] Non-root user in container
- [ ] `trivy image` zero HIGH/CRITICAL
- [ ] `.dockerignore` excludes build artifacts, `.env`, `.git/`
- [ ] No secrets baked into Docker image
- [ ] No `TODO`, stub placeholders, or `unimplemented` markers left in committed code

---

## Final Acceptance

> **Ref:** [spec.md#success-criteria](spec.md#success-criteria) for full checklist.

- [ ] All validation gates pass (including all spec compliance checks)
- [ ] Zero linter warnings, zero test failures
- [ ] Zero HIGH/CRITICAL CVEs
- [ ] Full flow works end-to-end
- [ ] Tenant isolation enforced (verified by security tests)
- [ ] All security headers present on every response
- [ ] Audit log complete for all mutations

**Final architecture compliance audit** — re-read [constitution.md](../constitution.md) and verify every rule is satisfied across the whole codebase, not just the phases where it was first checked. Pay particular attention to rules that apply globally (error handling, secret handling, input validation, layering) since they can be eroded incrementally across phases without any single gate catching it.

**Final design principles audit** — re-read [constitution.md#design-principles](../constitution.md#design-principles) and do a final sweep: YAGNI (no unused abstractions, no speculative generality), KISS (no unnecessary indirection, generics, or patterns), DRY (business rules have exactly one authoritative implementation), Single Responsibility (each module/function owns one thing), Functional Style (pure functions for business logic, no global mutable state), Parse Don't Validate (typed boundaries, enums for status fields), Fail Fast (errors returned immediately, no silent fallbacks), Least Surprise (functions do what their name says). Fix any violation found.

**Final spec compliance audit** — before marking complete, re-read [spec.md](spec.md), [design.md](design.md), [api.md](api.md), and [schema.md](schema.md) end-to-end and verify the implementation against each doc as a whole, not just the specific sections referenced by individual tasks. Check for: any endpoint or field defined in the docs but not implemented, any behavior in the implementation that contradicts the docs, any schema column or constraint that differs from the schema doc. If any discrepancy is found, it must be resolved and the relevant validation gate re-checked before final acceptance is granted.

**Docs sync check** — if the implementation revealed anything that forced a deviation from the original spec/design/schema docs (even a justified one), those docs must be updated to reflect reality before this gate is marked complete. The docs are the source of truth for future contributors; a doc that describes a design that was never built is worse than no doc.
