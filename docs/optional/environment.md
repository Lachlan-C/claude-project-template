# Environment & Dependencies

<!-- OPTIONAL DOC: Include this file only if your project needs environment variables,
     external service configuration, Docker, or has dependencies worth documenting.
     Most web apps, servers, bots, and deployed services need this.
     CLI tools, libraries, compilers, and self-contained projects usually don't.
     To use: copy this file to docs/environment.md and add it to the Related files headers
     in design.md, tasks.md, and the Canonical Sources table in CLAUDE.md. -->

> **Related files:** [../design.md](../design.md) (system design) | [../adr/](../adr/) (why these choices) | [../../CLAUDE.md](../../CLAUDE.md) (executable commands)
>
> **This doc owns:** Environment variables, dependencies with exact versions, build/deployment strategy.

---

## Prerequisites

| Tool | Version | Install |
|---|---|---|
| <!-- TODO: language runtime --> | <!-- version --> | <!-- install command --> |

<!-- OPTIONAL: Add if using Docker:
| Docker | 24+ | [docker.com](https://docs.docker.com/get-docker/) |
| Docker Compose | v2 | Bundled with Docker Desktop |
-->

---

## Environment Variables

Copy `.env.example` to `.env` and fill in values. The `.env` file must be gitignored.

```env
# TODO: Add your project's environment variables below.
# Only include this section if your project actually uses env vars.

# Examples by project type:
#
# Web app / API server:
# HOST=127.0.0.1
# PORT=3000
# DATABASE_URL=sqlite:data/app.db
# CSRF_SECRET=generate_a_random_32_byte_hex
#
# Bot:
# BOT_TOKEN=your_token_here
# WEBHOOK_SECRET=your_secret_here
#
# Service with external APIs:
# API_KEY=your_key_here
# API_BASE_URL=https://api.example.com
```

---

## Key Dependencies

```
<!-- TODO: list your dependencies with exact versions -->
<!-- e.g. for Rust (Cargo.toml): -->
<!-- e.g. for Node (package.json): -->
<!-- e.g. for Python (requirements.txt or pyproject.toml): -->
<!-- e.g. for Go (go.mod): -->
<!-- e.g. for C/C++ (CMakeLists.txt or Makefile): -->
```

---

<!-- OPTIONAL: Include this section only if your project uses Docker.

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
-->
