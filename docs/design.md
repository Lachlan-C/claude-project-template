# Design

> **Related files:** [spec.md](spec.md) (what & why) | [adr/](adr/) (why we chose each technology) | [tasks.md](tasks.md) (task checklist) | [../CLAUDE.md](../CLAUDE.md) (routing protocol) | [../constitution.md](../constitution.md) (immutable rules)
>
> **This doc owns:** System architecture diagram, component responsibilities, project file structure, implementation phases. Technology choices and rationale live in [adr/](adr/).

<!-- OPTIONAL DOCS: Add these to the Related files header if your project uses them:
     | [environment.md](environment.md) (env vars, deps) | [schema.md](schema.md) (data model) | [api.md](api.md) (endpoints) -->

## Overview

<!-- TODO: 2-3 sentences describing the system and its high-level approach. -->

## Architecture

```
<!-- TODO: ASCII diagram of your system's components/layers.
     Examples for different project types:

     CLI tool:
     ┌──────────────┐
     │  CLI Parser   │
     ├──────────────┤
     │  Core Logic   │
     ├──────────────┤
     │  I/O Layer    │
     └──────────────┘

     Library:
     ┌──────────────┐
     │  Public API   │
     ├──────────────┤
     │  Core Engine  │
     ├──────────────┤
     │  Internal     │
     └──────────────┘

     Web app:
     ┌──────────────┐
     │  Routes/UI    │
     ├──────────────┤
     │  Services     │
     ├──────────────┤
     │  Data Layer   │
     └──────────────┘

     Compiler/interpreter:
     ┌──────────────┐
     │  Parser       │
     ├──────────────┤
     │  AST / IR     │
     ├──────────────┤
     │  Codegen/Eval │
     └──────────────┘
-->
```

## Technology Stack

| Component | Choice | Reason |
|---|---|---|
| Language | TODO | TODO |
| <!-- TODO: add rows for your project's key components --> | | |

Rationale for each choice: see [adr/](adr/).

---

## Security Considerations

<!-- TODO: Fill in the sections relevant to your project type. Delete the rest.
     Not every project needs all of these — a CLI tool won't need auth middleware,
     a compiler won't need HTTP security headers. Keep only what applies. -->

<!-- OPTIONAL: For projects with user-facing network interfaces (web apps, APIs, servers):

### Authentication & Authorization
- **Auth method:** [JWT / session / API key / etc.]
- **Isolation:** Every query scoped to authenticated user's context

### HTTP Security (middleware stack)
| Layer | Library/Config | Protection |
|---|---|---|
| Rate limiting | TODO | IP-based, N req/min |
| Security headers | TODO | X-Content-Type-Options, X-Frame-Options, CSP, HSTS |
| Auth | TODO | Verify token on protected routes |

### Security Headers (set on every response)
```
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
Content-Security-Policy: default-src 'self'
Strict-Transport-Security: max-age=63072000; includeSubDomains
```
-->

### General Security
- No plaintext secrets in code, logs, or version control
- Input validation at system boundaries
- Secrets via env vars or config files (gitignored)

<!-- OPTIONAL: For projects with persistent storage:
### Data Security
- All queries use parameterized bindings (no string interpolation)
- Audit trail: every mutation logged with actor, action, resource, timestamp
-->

<!-- OPTIONAL: For containerized/deployed projects:
### Infrastructure Security
- Non-root container
- No shell in production container
- Read-only filesystem in production
- Secrets via env vars: no secrets in code, Docker image, or git
-->

---

<!-- OPTIONAL: For projects with persistent data storage. Delete if not applicable.
## Data Model

See [schema.md](schema.md) for the single source of truth on all data schemas.

**Key architecture decisions** (owned by this doc):
- **[Decision]:** Reason

---
-->

## Project Structure

```
project-root/
├── <!-- TODO: fill in your project's actual file structure -->
├── src/                    # (or equivalent for your language)
│   ├── main.[ext]          # Entry point
│   └── ...
└── tests/
```

---

## Implementation Phases

### Phase 1: Project Skeleton
- Initialize project with dependencies
- Set up entry point and basic structure
- Verify build/run/test cycle works

### Phase 2: Core Feature(s)
<!-- TODO: break into phases that match your domain -->

### Phase N: Testing & Hardening
- Comprehensive tests (unit + integration)
- Edge case coverage
- Dependency audit

<!-- OPTIONAL: For deployable projects:
### Phase N+1: Packaging & Deployment
- Build optimization
- Distribution packaging (Docker / binary / package registry)
-->

---

## Phase Dependency Graph

```
Phase 1: Skeleton ─────────────────────────┐
    │                                       │
    ├── Phase 2: Core ─────────────┐       │
    │                              │       │
    └─────────────────────────────┴─── Phase N: Testing
```
