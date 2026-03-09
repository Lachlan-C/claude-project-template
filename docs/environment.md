# Environment & Dependencies

> **Related files:** [design.md](design.md) (system design) | [adr/](adr/) (why these choices) | [../CLAUDE.md](../CLAUDE.md) (executable commands)
>
> **This doc owns:** Environment variables, dependencies with exact versions, Docker build strategy.

---

## Prerequisites

| Tool | Version | Install |
|---|---|---|
| <!-- TODO: language runtime --> | <!-- version --> | <!-- install command --> |
| Docker | 24+ | [docker.com](https://docs.docker.com/get-docker/) |
| Docker Compose | v2 | Bundled with Docker Desktop |

---

## Environment Variables

Copy `.env.example` to `.env` and fill in values. The `.env` file must be gitignored.

```env
# App
HOST=127.0.0.1
PORT=3000

# Database
DATABASE_URL=<!-- e.g. sqlite:data/app.db or postgres://... -->

# Auth
# TODO: add auth-related vars

# External Services
# TODO: add service API keys, webhook secrets, etc.

# Security
CSRF_SECRET=generate_a_random_32_byte_hex
```

---

## Key Dependencies

```
<!-- TODO: list your dependencies with exact versions -->
<!-- e.g. for Rust (Cargo.toml): -->
<!-- e.g. for Node (package.json): -->
<!-- e.g. for Python (requirements.txt or pyproject.toml): -->
```

---

## Docker Strategy

### Dockerfile

**Build stage:**
```dockerfile
# TODO: fill in your build stage
# e.g. FROM rust:1.85-alpine AS builder
# e.g. FROM node:22-alpine AS builder
# e.g. FROM python:3.12-slim AS builder
```

**Runtime stage:**
```dockerfile
# TODO: fill in your minimal runtime stage
# e.g. FROM gcr.io/distroless/static-debian12:nonroot
# e.g. FROM gcr.io/distroless/nodejs22-debian12:nonroot
```

**Security hardening:**
- No shell in container (distroless/scratch) — prevents shell injection
- Non-root user — limits privilege escalation
- Read-only filesystem in deployment
- `trivy image` scan for zero CVEs
- No unnecessary packages in runtime image
