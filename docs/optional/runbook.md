# Runbook

<!-- OPTIONAL DOC: Include this file only if your project is deployed and runs continuously —
     a server, bot, background worker, database, or any long-running service.
     CLI tools, libraries, compilers, and one-shot scripts don't need this.
     To use: copy this file to docs/runbook.md and add it to the Related files headers
     in design.md, tasks.md, and the Canonical Sources table in CLAUDE.md. -->

> **Related files:** [../design.md](../design.md) (system design) | [../adr/](../adr/) (why these choices) | [../../CLAUDE.md](../../CLAUDE.md) (executable commands)
>
> If your project has an environment doc: | [environment.md](environment.md) (env vars, dependencies)

This document covers deployment, health checks, common failure modes, and recovery procedures. **Read this before making any change to a running service.**

> **This doc owns:** Deployment steps, health check commands, alert definitions, and recovery procedures. `environment.md` owns env vars and dependency versions — this doc references them but doesn't repeat them.

---

## Service Overview

| Property | Value |
|---|---|
| Process name | <!-- TODO: e.g. `myapp` --> |
| Default port | <!-- TODO: e.g. `3000` --> |
| Start command | <!-- TODO: e.g. `./myapp` / `npm start` / `python -m app` --> |
| Stop command | <!-- TODO: e.g. `kill -TERM <pid>` / `systemctl stop myapp` --> |
| Restart command | <!-- TODO: e.g. `systemctl restart myapp` --> |
| Logs | <!-- TODO: e.g. `journalctl -u myapp -f` / `tail -f logs/app.log` --> |
| Config | <!-- TODO: e.g. `.env` file — see environment.md --> |

---

## Deployment

### First Deploy

```bash
# TODO: fill in first-time setup steps
# 1. Clone repo and install dependencies
# 2. Copy .env.example to .env and fill in values
# 3. Run migrations (if applicable)
# 4. Start service
```

### Update / Redeploy

```bash
# TODO: fill in update steps
# 1. Pull latest code
# 2. Run migrations (if applicable)
# 3. Restart service
```

### Rollback

```bash
# TODO: fill in rollback steps
# 1. Check out previous version
# 2. Restart service
# Note: if migrations were applied, reverse them first
```

---

## Health Checks

| Check | Command | Expected output |
|---|---|---|
| Process running | <!-- TODO: e.g. `pgrep myapp` --> | Non-zero PID |
| HTTP health | <!-- TODO: e.g. `curl -sf http://localhost:3000/health` --> | `{"status":"ok"}` |
| DB reachable | <!-- TODO: e.g. `sqlite3 data/app.db "SELECT 1"` --> | `1` |

**Automated check interval:** <!-- TODO: e.g. every 30 seconds via systemd, cron, or uptime monitor -->

---

## Alerts

| Alert | Condition | Severity | Response |
|---|---|---|---|
| Service down | Health check fails 3× in a row | Critical | See [Service Won't Start](#service-wont-start) |
| High error rate | <!-- TODO: e.g. >1% 5xx in 5 min --> | High | Check logs; see [High Error Rate](#high-error-rate) |
| <!-- TODO --> | | | |

---

## Common Failure Modes

### Service Won't Start

```bash
# 1. Check logs for startup errors
# TODO: e.g. journalctl -u myapp -n 50

# 2. Verify env vars are set
# TODO: e.g. grep -v '^#' .env | grep -v '^$'

# 3. Check port is free
# TODO: e.g. lsof -i :3000

# 4. Check dependencies are reachable
# TODO: e.g. ping db-host / curl external-api
```

**Common causes:**
- Missing or malformed env var — check `environment.md` for required vars
- Port already in use — kill existing process or change `PORT`
- DB file missing or corrupted — restore from backup (see [Backup & Restore](#backup--restore))

---

### High Error Rate

```bash
# 1. Check recent errors
# TODO: e.g. journalctl -u myapp --since "5 minutes ago" | grep ERROR

# 2. Check if a specific endpoint is failing
# TODO: describe how to identify the failing path from logs

# 3. Check external dependencies
# TODO: e.g. is the DB responding? is the third-party API up?
```

**Common causes:**
- Downstream API outage — check their status page; fail gracefully if possible
- DB lock / connection exhaustion — restart service; check for long-running queries
- Bad deploy — rollback (see [Rollback](#rollback))

---

### Out of Disk Space

```bash
# 1. Check disk usage
df -h

# 2. Find large files
du -sh /* 2>/dev/null | sort -rh | head -20

# 3. Rotate or truncate logs
# TODO: e.g. truncate -s 0 logs/app.log
```

---

<!-- OPTIONAL: Include if your project has persistent data worth backing up.

## Backup & Restore

### Backup

```bash
# TODO: fill in backup command
# e.g. for SQLite: cp data/app.db backups/app-$(date +%Y%m%d-%H%M%S).db
# e.g. for Postgres: pg_dump mydb > backups/mydb-$(date +%Y%m%d-%H%M%S).sql
```

**Backup schedule:** <!-- TODO: e.g. daily via cron, retained for 30 days -->

### Restore

```bash
# TODO: fill in restore command
# WARNING: this overwrites current data
# 1. Stop service
# 2. Restore backup file
# 3. Start service
```

-->

---

## Maintenance Tasks

| Task | Frequency | Command |
|---|---|---|
| Rotate logs | <!-- TODO: e.g. weekly --> | <!-- TODO --> |
| Prune old data | <!-- TODO: e.g. monthly --> | <!-- TODO --> |
| Dependency updates | <!-- TODO: e.g. monthly --> | <!-- TODO: e.g. `npm audit fix` --> |
