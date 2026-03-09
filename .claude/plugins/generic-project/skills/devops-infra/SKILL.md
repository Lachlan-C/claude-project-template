---
name: devops-infra
description: >
  This skill should be used when the user works on the "Dockerfile", "docker compose",
  "Docker image", "deployment", "environment variable", ".env" configuration,
  "container" configuration, "CI/CD pipeline", or infrastructure configuration.
---

# DevOps & Infrastructure

## Docker Runtime Constraints

| Constraint | Requirement |
|---|---|
| User | Non-root (use a dedicated app user) |
| Filesystem | Read-only in production; only data directories writable |
| Secrets | Never baked into Docker images |
| Debugging | stdout/stderr logs only in production |

### Multi-Stage Build Pattern

Use a multi-stage build to keep the production image small and clean:

```dockerfile
# Stage 1: build
FROM <language-runtime> AS builder
# install deps, compile, etc.

# Stage 2: runtime
FROM <minimal-base> AS runtime
# copy only the built artifact
# set non-root user
# expose port
```

Target production image size: as small as feasible for the runtime.

## Environment Variables

All secrets live in `.env` (gitignored). Reference via environment variables — never hardcode values in source code or Docker images.

Standard variables every project should define:

| Variable | Purpose |
|---|---|
| `HOST` | Bind address (e.g. `0.0.0.0`) |
| `PORT` | Listen port |
| `DATABASE_URL` | Primary database connection string |
| `CSRF_SECRET` | CSRF token signing secret |

Add project-specific variables to `docs/environment.md`.

## Docker Compose

For local development, define services in `docker-compose.yml`:

- Health checks on all services
- Volume mounts for persistent data
- Restart policies (`unless-stopped`)
- No host networking — use Docker bridge network
- Secrets injected via env file, not hardcoded in compose

## Checklist Before Submitting Infrastructure Changes

- [ ] Container runs as non-root user.
- [ ] No secrets baked into Docker images.
- [ ] No secrets hardcoded in `docker-compose.yml`.
- [ ] Production image uses minimal base (no shell/package manager if possible).
- [ ] All required env vars are documented in `docs/environment.md`.
- [ ] Health checks defined for all services.
