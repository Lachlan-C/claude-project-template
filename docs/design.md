# Design

> **Related files:** [spec.md](spec.md) (what & why) | [adr/](adr/) (why we chose each technology) | [environment.md](environment.md) (env vars, deps, Docker) | [tasks.md](tasks.md) (task checklist) | [schema.md](schema.md) (tables & fields) | [api.md](api.md) (endpoints) | [../CLAUDE.md](../CLAUDE.md) (routing protocol) | [../constitution.md](../constitution.md) (immutable rules)
>
> **This doc owns:** System architecture diagram, layer responsibilities, security architecture, project file structure. Technology choices and rationale live in [adr/](adr/).

## Overview

<!-- TODO: 2-3 sentences describing the system and its high-level approach. -->

## Architecture

```
<!-- TODO: ASCII diagram of your system layers/components -->

┌─────────────────────────────────────────────────┐
│  Presentation Layer                             │
│  - [framework/tech]                             │
├─────────────────────────────────────────────────┤
│  Service Layer (Business Logic)                 │
│  - [what lives here]                            │
├─────────────────────────────────────────────────┤
│  Data Layer                                     │
│  - [databases, external services]               │
└─────────────────────────────────────────────────┘
```

## Technology Stack

| Component | Choice | Reason |
|---|---|---|
| Web framework | TODO | TODO |
| Database | TODO | TODO |
| Auth | TODO | TODO |
| Templates/Frontend | TODO | TODO |

Rationale for each choice: see [adr/](adr/).

---

## Security Architecture

### Authentication & Authorization
<!-- TODO: describe auth mechanism -->
- **Auth method:** [JWT / session / API key / etc.]
- **Org/tenant isolation:** Every query scoped to authenticated user's context

### HTTP Security (middleware stack)
```
Request → [Layer 1] → [Layer 2] → [Layer 3] → Handler
<!-- TODO: fill in your actual middleware stack in order -->
```

| Layer | Crate/Config | Protection |
|---|---|---|
| Rate limiting | TODO | IP-based, N req/min |
| Security headers | TODO | X-Content-Type-Options, X-Frame-Options, CSP, HSTS |
| Auth | TODO | Verify token on protected routes |
| Body size limit | TODO | Prevent DoS |

### Security Headers (set on every response)
```
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
X-XSS-Protection: 0
Content-Security-Policy: default-src 'self'
Strict-Transport-Security: max-age=63072000; includeSubDomains
Referrer-Policy: strict-origin-when-cross-origin
Permissions-Policy: camera=(), microphone=(), geolocation=()
```

### Data Security
- No plaintext secrets: passwords hashed, API keys hashed before storage
- SQL injection prevention: all queries use parameterized bindings
- Input validation: all user input validated at service layer boundary
- Audit trail: every mutation logged with actor, action, resource, IP, timestamp

### Infrastructure Security
- Non-root container
- No shell in production container
- Read-only filesystem in production
- Secrets via env vars: no secrets in code, Docker image, or git

---

## Data Model

See [schema.md](schema.md) for the single source of truth on all table schemas, field types, constraints, indexes, and invariants.

**Key architecture decisions** (owned by this doc):
<!-- TODO: list 2-3 key data model decisions -->
- **[Decision]:** Reason

---

## Project Structure

```
project-root/
├── <!-- TODO: fill in your project's file structure -->
├── src/
│   ├── main.[ext]           # Entry point
│   ├── config.[ext]         # Environment/config loading
│   ├── error.[ext]          # Error types
│   ├── models.[ext]         # Data structures
│   ├── middleware/
│   ├── routes/
│   └── services/
└── tests/
```

---

## Implementation Phases

### Phase 1: Project Skeleton & Database
- Initialize project with dependencies
- Set up server with health check
- Configure database, run migrations
- Docker Compose for local dev

### Phase 2: Auth & Security Middleware
- Auth implementation
- Security headers middleware
- CSRF protection
- Rate limiting

### Phase 3: Core Feature(s)
<!-- TODO: break into phases that match your domain -->

### Phase N: Testing, Security & Hardening
- Integration tests (full flow end-to-end)
- Security tests (auth bypass, CSRF, rate limiting, tenant isolation)
- Dependency audit

### Phase N+1: Docker & Deployment
- Multi-stage Dockerfile
- Production docker-compose
- Non-root user, read-only filesystem

---

## Phase Dependency Graph

```
Phase 1: Skeleton ──────────────────────────────┐
    │                                           │
    ├── Phase 2: Auth ──────────────────────┐   │
    │       │                               │   │
    │       ├── Phase 3: Core ─────────┐   │   │
    │       │                          │   │   │
    │       └──────────────────────────┴───┴── Phase N: Testing ──┐
    │                                                              │
    └─────────────────────────────────── Phase N+1: Docker ───────┘
```
