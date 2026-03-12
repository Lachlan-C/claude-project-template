# [Project Name] - Tasks

> **Related files:** [design.md](design.md) (system design & phases) | [adr/](adr/) (why) | [spec.md](spec.md) (acceptance criteria) | [../constitution.md](../constitution.md) (immutable rules)
>
> **This doc owns:** Task sequence, parallelism markers, file paths, validation gates.

<!-- OPTIONAL DOCS: Add these to the Related files header if your project uses them:
     | [environment.md](environment.md) (env/deps) | [schema.md](schema.md) (data model) | [api.md](api.md) (endpoints) -->

Sequential task IDs. Complete in order unless marked `[parallel]`.
After each task, run the quality gate checks before moving on.

## Quality Gate (run after every task)

```bash
# TODO: replace with your quality gate
# e.g. Rust:   cargo check && cargo fmt && cargo clippy -- -D warnings && cargo test
# e.g. Node:   npm run lint && npm test
# e.g. Python: ruff check . && pytest
# e.g. Go:     go vet ./... && go test ./...
# e.g. C/C++:  make && make test
```

---

## Phase 1: Project Skeleton

> **Goal:** Project compiles/runs, basic structure in place, build/test cycle works.
> **Key docs:** [design.md](design.md) for project structure.

- [ ] **T001** Initialize project
  - Files: `[Cargo.toml / package.json / pyproject.toml / Makefile / go.mod]`, `src/main.[ext]`
- [ ] **T002** Add dependencies `[parallel: T001]`
- [ ] **T003** Write entry point — minimal working program
  - Files: `src/main.[ext]`
- [ ] **T004** Create module/package scaffolding `[parallel: T003]`
  - Files: project structure per [design.md#project-structure](design.md#project-structure)
- [ ] **T005** Write error types/handling
  - Files: `src/error.[ext]`
- [ ] **T005b** Write first test — verify basic functionality `[parallel: T005]`

### Validation Gate 1
- [ ] Quality gate passes
- [ ] Project builds and runs successfully
- [ ] At least one test passes
- [ ] Project structure matches [design.md#project-structure](design.md#project-structure)
- [ ] No `TODO`, stub placeholders, or `unimplemented` markers left in committed code
- [ ] **Language & stack check** — verify only approved languages, frameworks, and dependencies per [constitution.md#language--stack](../constitution.md#language--stack) are present.
- [ ] **Design principles check** — read [constitution.md#design-principles](../constitution.md#design-principles) and review: no abstractions for single consumers, no speculative config, no unnecessary helpers.

<!-- OPTIONAL: Add these tasks/gates if your project has a database:
- [ ] **T0XX** Write database setup — connection pool + migration runner
- [ ] **T0XX** Create initial migration — core tables + indexes (ref: schema.md)
- [ ] Database migrations applied (all tables exist)
- [ ] **Spec compliance check** — verify migration files match schema.md exactly
-->

<!-- OPTIONAL: Add these tasks/gates if your project needs env var config:
- [ ] **T0XX** Create `.env.example` and `.env` (gitignored)
- [ ] **T0XX** Write config loading with validation on startup
- [ ] `.env` is in `.gitignore`
-->

---

## Phase 2: Core Feature(s)

> **Goal:** [Describe the core domain feature]
> **Key docs:** [spec.md](spec.md) for acceptance criteria.

<!-- TODO: break down your core feature into tasks following the same pattern -->

- [ ] **T006** Implement [core feature] — main logic
  - Files: `src/[module].[ext]`
- [ ] **T006b** Write unit tests for [core feature] `[parallel: T007]`
  - Tests: happy path, edge cases, error cases
- [ ] **T007** Implement [supporting feature] `[parallel: T006]`
- [ ] **T008** Write integration/end-to-end tests

### Validation Gate 2
- [ ] Quality gate passes
- [ ] All Phase 1 validation gate items still pass (regression guard)
- [ ] Core feature works end-to-end
- [ ] Unit tests pass for all logic
- [ ] No `TODO`, stub placeholders, or `unimplemented` markers left in committed code
- [ ] **Spec compliance check** — read [spec.md](spec.md) acceptance criteria, verify each is satisfied by actual code (not just by a passing test).
- [ ] **Design principles check** — read [constitution.md#design-principles](../constitution.md#design-principles): pure functions for business logic, domain concepts use appropriate types, each function does one thing, no duplicated logic.

<!-- OPTIONAL: Add these gates if your project has an API:
- [ ] All endpoint paths/methods/request/response shapes match api.md
-->

<!-- OPTIONAL: Add these gates if your project has a database:
- [ ] All schema fields written to the DB match schema.md
- [ ] Audit log entries created for all mutations (if applicable)
-->

---

## Phase N: Testing & Hardening

> **Goal:** Comprehensive tests, edge cases, code quality sweep.
> **Key docs:** [spec.md#success-criteria](spec.md#success-criteria), [constitution.md](../constitution.md).

- [ ] **TN01** Write comprehensive tests — cover all edge cases and error paths
- [ ] **TN02** Run dependency audit — fix any CVE or license issues `[parallel: TN01]`
- [ ] **TN03** Code review: grep for unsafe error handling, plaintext secrets, missing validation `[parallel: TN02]`

### Validation Gate N
- [ ] Quality gate passes
- [ ] All prior validation gate items still pass (regression guard)
- [ ] No unsafe error handling on user input, no plaintext secrets
- [ ] No `TODO`, stub placeholders, or `unimplemented` markers left in committed code
- [ ] **Architecture compliance check** — read [constitution.md](../constitution.md) in full, verify the entire codebase follows all rules. This is structural — it catches coupling and layering violations that passing tests won't catch.
- [ ] **Spec compliance check** — read [spec.md#success-criteria](spec.md#success-criteria) and [spec.md#key-properties](spec.md#key-properties), walk through every item against the actual implementation.
- [ ] **Testing quality check** — read [constitution.md#testing](../constitution.md#testing) and audit: every key function has at least one test, test names follow consistent pattern, no tests that just restate implementation.
- [ ] **Dead code check** — no modules, functions, or types exist without a current consumer; no commented-out code. Linter reports zero dead code warnings.
- [ ] **Design principles check** — full codebase sweep per [constitution.md#design-principles](../constitution.md#design-principles): no premature abstractions, no single-use interfaces, no speculative extension points, no global mutable state.

<!-- OPTIONAL: For projects with network interfaces:
- [ ] Security tests pass (auth bypass, rate limiting, input validation)
- [ ] Graceful shutdown: server drains in-flight requests
-->

<!-- OPTIONAL: For deployable/containerized projects:

---

## Phase N+1: Packaging & Deployment

> **Goal:** Production-ready build/package with minimal attack surface.
> **Key docs:** [environment.md#docker-strategy](environment.md#docker-strategy) (if Docker), [constitution.md#docker](../constitution.md#docker--deployment).

- [ ] **TN101** Add release/production build optimizations
- [ ] **TN102** Write packaging config (Dockerfile, .dockerignore, release script, etc.)
- [ ] **TN103** Build and test production artifact
- [ ] **TN104** Verify production artifact works correctly

### Validation Gate N+1
- [ ] Production build succeeds
- [ ] All prior validation gate items still pass (regression guard)
- [ ] No secrets baked into build artifact
- [ ] No `TODO`, stub placeholders, or `unimplemented` markers left in committed code
-->

---

## Final Acceptance

> **Ref:** [spec.md#success-criteria](spec.md#success-criteria) for full checklist.

- [ ] All validation gates pass
- [ ] Zero linter warnings, zero test failures
- [ ] Full flow works end-to-end

**Final architecture compliance audit** — re-read [constitution.md](../constitution.md) and verify every rule is satisfied across the whole codebase. Pay particular attention to rules that apply globally (error handling, secret handling, input validation) since they can be eroded incrementally across phases.

**Final design principles audit** — re-read [constitution.md#design-principles](../constitution.md#design-principles) and do a final sweep: YAGNI, KISS, DRY, Single Responsibility, Parse Don't Validate, Fail Fast, Least Surprise.

**Final spec compliance audit** — re-read [spec.md](spec.md) and [design.md](design.md) end-to-end and verify the implementation against each doc as a whole. Check for any behavior defined in docs but not implemented, or any implementation that contradicts the docs.

**Docs sync check** — if the implementation forced deviations from original docs, update them to reflect reality. The docs are the source of truth for future contributors.
