# [Project Name] - Product Spec

> **Related files:** [design.md](design.md) (how) | [adr/](adr/) (why) | [tasks.md](tasks.md) (task checklist) | [schema.md](schema.md) (tables) | [api.md](api.md) (endpoints) | [../constitution.md](../constitution.md) (immutable rules)
>
> **This doc owns:** User flows, acceptance criteria, edge cases, non-goals. It references but never duplicates endpoint contracts ([api.md](api.md)), schema details ([schema.md](schema.md)), or architecture decisions ([design.md](design.md)).

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

### Security
- [ ] Zero HIGH/CRITICAL CVEs
- [ ] Auth enforced on all protected routes
- [ ] No plaintext secrets in logs, responses, or database

### Operational
- [ ] Server starts in < [N] seconds, health check at `/health`
- [ ] Graceful shutdown

### Data Integrity
- [ ] [Key invariant holds, e.g. ledger balances]
- [ ] Audit log is append-only
