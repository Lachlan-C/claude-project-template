# Threat Model

<!-- OPTIONAL DOC: Include this file only if your project handles authentication, user data,
     money, secrets, network input, or any untrusted input that could affect system state.
     Web apps, APIs, databases, bots, browsers, network stacks, and shells need this.
     CLI tools, compilers, libraries with no network surface usually don't.
     To use: copy this file to docs/threat-model.md and add it to the Related files headers
     in design.md, spec.md, tasks.md, and the Canonical Sources table in CLAUDE.md. -->

> **Related files:** [../design.md](../design.md) (system design) | [../spec.md](../spec.md) (what we're building) | [../../constitution.md](../../constitution.md) (immutable security rules)
>
> If your project has an API: | [api.md](api.md) (attack surface) | If it has a schema: | [schema.md](schema.md) (sensitive data)

This document is the single source of truth for security threats, trust boundaries, and mitigations. **Read this before implementing any feature that touches auth, input handling, secrets, or data access.**

> **This doc owns:** Trust boundaries, actor threat levels, threat catalogue, mitigations, and security invariants. `constitution.md` owns the coding rules that enforce these mitigations — this doc explains the *why*.

---

## System Diagram

```
<!-- TODO: draw your trust boundary diagram in ASCII or describe it in prose.
     Show: external actors, entry points, trust boundaries, data stores.

Example:

  [Internet / untrusted]
        |
        v
  [ Load Balancer ]
        |
        v
  [ App Server ] -----> [ Database (trusted internal) ]
        |
        v
  [ External API (semi-trusted) ]
-->
```

---

## Actors & Trust Levels

| Actor | Trust level | Description |
|---|---|---|
| Unauthenticated user | Untrusted | Anyone on the internet |
| Authenticated user | Low trust | Verified identity, but must not access others' data |
| Admin | Medium trust | Elevated permissions; actions are audited |
| Internal service | High trust | Own infrastructure; still validated at boundary |
| <!-- TODO --> | | |

---

## Assets to Protect

| Asset | Sensitivity | Location |
|---|---|---|
| <!-- TODO: e.g. User credentials --> | Critical | <!-- TODO: e.g. `users` table, hashed --> |
| <!-- TODO: e.g. Session tokens --> | High | <!-- TODO: e.g. httpOnly cookies --> |
| <!-- TODO: e.g. User PII --> | High | <!-- TODO: e.g. `users` table --> |
| <!-- TODO: e.g. API keys / secrets --> | Critical | <!-- TODO: e.g. env vars, never in DB --> |

---

## Threat Catalogue

Using STRIDE: **S**poofing, **T**ampering, **R**epudiation, **I**nformation disclosure, **D**enial of service, **E**levation of privilege.

### Authentication & Session

| ID | Threat | Category | Mitigation | Status |
|---|---|---|---|---|
| T01 | Session token stolen via XSS | I | `httpOnly` + `Secure` cookies; strict CSP | <!-- TODO: implemented / planned --> |
| T02 | Session fixation | S | Rotate session ID on login | <!-- TODO --> |
| T03 | Brute-force login | S | Rate-limit auth endpoints (10 req/min per IP) | <!-- TODO --> |
| T04 | Credential stuffing | S | Same rate limiting; optionally CAPTCHA | <!-- TODO --> |

### Input & Injection

| ID | Threat | Category | Mitigation | Status |
|---|---|---|---|---|
| T10 | SQL injection | T/I | Parameterised queries only — never string-format SQL | <!-- TODO --> |
| T11 | XSS (stored) | T | Escape all user content at render; strict CSP | <!-- TODO --> |
| T12 | XSS (reflected) | T | Same as above; no user input in HTML without escaping | <!-- TODO --> |
| T13 | Path traversal | I | Validate/strip `../` sequences before any file operation | <!-- TODO --> |
| T14 | Command injection | T/E | Never pass user input to shell; use library APIs | <!-- TODO --> |

### Access Control

| ID | Threat | Category | Mitigation | Status |
|---|---|---|---|---|
| T20 | Horizontal privilege escalation (IDOR) | I/E | Check ownership on every resource access | <!-- TODO --> |
| T21 | CSRF | T | CSRF token on all state-changing requests | <!-- TODO --> |
| T22 | Insecure direct object reference | I | Authorise before returning any resource | <!-- TODO --> |

### Secrets & Data

| ID | Threat | Category | Mitigation | Status |
|---|---|---|---|---|
| T30 | Secret leaked in logs | I | Scrub secrets from all log output | <!-- TODO --> |
| T31 | Secret in source control | I | Secrets in env vars only; `.env` gitignored | <!-- TODO --> |
| T32 | Sensitive data in error responses | I | Generic error messages to clients; detail in server logs only | <!-- TODO --> |

### Availability

| ID | Threat | Category | Mitigation | Status |
|---|---|---|---|---|
| T40 | Payload too large | D | Enforce request size limits | <!-- TODO --> |
| T41 | Slow client / request flooding | D | Timeouts and per-IP rate limits | <!-- TODO --> |

<!-- TODO: add project-specific threats -->

---

## Security Invariants

These must never be violated. Any code change that would break an invariant requires an ADR.

- Secrets (tokens, keys, passwords) are **never** logged, serialised to JSON responses, or written to the DB in plaintext.
- Every state-changing endpoint verifies auth **before** processing the request body.
- Every resource access checks ownership/permission — not just authentication.
- User input is **never** interpolated into SQL, shell commands, file paths, or HTML without escaping/parameterisation.
- CSRF tokens are validated on every non-idempotent HTTP request.
- <!-- TODO: add project-specific invariants -->

---

## Out of Scope

The following threats are explicitly not mitigated by this project:

- <!-- TODO: e.g. Nation-state attacks / zero-days in runtime -->
- <!-- TODO: e.g. Physical access to servers -->
- <!-- TODO: e.g. Compromise of third-party dependencies -->
