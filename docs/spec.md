# [Project Name] - Product Spec

> **Related files:** [design.md](design.md) (how) | [adr/](adr/) (why) | [tasks.md](tasks.md) (task checklist) | [../constitution.md](../constitution.md) (immutable rules)
>
> **This doc owns:** User flows, acceptance criteria, edge cases, non-goals. It references but never duplicates architecture decisions ([design.md](design.md)) or technology rationale ([adr/](adr/)).

<!-- OPTIONAL DOCS: Add these to the Related files header if your project uses them:
     | [schema.md](schema.md) (data model) | [api.md](api.md) (endpoints) | [environment.md](environment.md) (env/deps) -->

## What

<!-- TODO: One paragraph describing what the system does. -->

## Why

<!-- TODO: Why does this exist? What problem does it solve? What alternatives were considered and rejected? -->

## Target Users

<!-- TODO: Who uses this? -->
- **[User type 1]** — description
- **[User type 2]** — description

---

## Core User Flows

### Flow 1: [Name]

**Actor:** [User type]

1. Step one
2. Step two
3. Step three

**Acceptance criteria:**
- Criterion one
- Criterion two

**Edge cases:**
- Edge case one → expected behavior
- Edge case two → expected behavior

**Error states:**
- Error condition → expected response

### Flow 2: [Name]

**Actor:** [User type]

<!-- TODO: repeat pattern above -->

---

## Key Properties

<!-- TODO: List the non-negotiable system properties -->
- **[Property name]:** Description
- **[Property name]:** Description

---

## Non-Goals (v1)

<!-- TODO: Explicitly list what this version does NOT do -->
- Feature X
- Feature Y

---

## Success Criteria

### Functional
- [ ] [Core flow works end-to-end]
- [ ] [Key feature works correctly]

### Quality
- [ ] Zero linter warnings, zero test failures
- [ ] No plaintext secrets in logs, responses, or storage

### Operational
- [ ] Project builds and runs successfully
- [ ] Graceful error handling on all user-facing paths

<!-- OPTIONAL: Add sections below based on your project type.

### Security (for networked/deployed projects)
- [ ] Zero HIGH/CRITICAL CVEs
- [ ] Auth enforced on all protected routes

### Data Integrity (for projects with persistent storage)
- [ ] [Key invariant holds]
- [ ] Audit log is append-only

### Performance (if applicable)
- [ ] [Benchmark target, e.g. "parses 10k lines/sec"]
-->
