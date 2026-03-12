# Schema / Data Model Reference

<!-- OPTIONAL DOC: Include this file only if your project uses a persistent data store
     (SQL database, NoSQL, file-based storage with defined schemas, etc.).
     To use: copy this file to docs/schema.md and add it to the Related files headers
     in design.md, spec.md, tasks.md, and the Canonical Sources table in CLAUDE.md. -->

> **Related files:** [../design.md](../design.md) (system design) | [../adr/](../adr/) (why these choices) | [../../constitution.md](../../constitution.md) (immutable rules)
>
> If your project also has an API: | [api.md](api.md) (endpoints)

This document is the single source of truth for all data schemas — tables, collections, file formats, or any structured persistent data. **Read this before modifying any data structure.**

> **This doc owns:** All data schemas, field types, constraints, indexes, invariants, atomic operation steps. Other docs reference schema details here by link — they never repeat field definitions or invariants inline.

---

## Conventions

| Convention | Rule |
|---|---|
| Primary keys | <!-- TODO: TEXT UUIDs / auto-increment integers / etc. --> |
| Money | <!-- TODO: INTEGER micro-units / cents — never float --> |
| Timestamps | <!-- TODO: ISO 8601 strings / unix epoch / etc. --> |
| Booleans | <!-- TODO: native bool / INTEGER 0-1 --> |
| Enums | <!-- TODO: TEXT with CHECK constraints / native enum type --> |
| Soft deletes | <!-- TODO: used or not used --> |
| Indexes | On all foreign keys and common query patterns |

---

## Tables

### `[table_name]`

<!-- TODO: describe what this table represents -->

| Column | Type | Constraints | Notes |
|---|---|---|---|
| id | TEXT | PRIMARY KEY | UUID v4 |
| created_at | TEXT | NOT NULL, DEFAULT | ISO 8601 timestamp |
| <!-- TODO --> | | | |

**Indexes:**
- `idx_[table]_[column]` on `[column]` — reason

**Invariants:**
- <!-- TODO: list data invariants that must always hold -->

### `audit_log`

Immutable append-only log of every mutation in the system.

| Column | Type | Constraints | Notes |
|---|---|---|---|
| id | TEXT | PRIMARY KEY | UUID v4 |
| actor_id | TEXT | | User/system who performed action |
| actor_type | TEXT | NOT NULL | `user`, `api_key`, `system`, `webhook` |
| action | TEXT | NOT NULL | Dot-notation: `resource.verb` |
| resource_type | TEXT | NOT NULL | e.g. `payment`, `user` |
| resource_id | TEXT | NOT NULL | ID of affected resource |
| details | TEXT | | JSON with context (before/after, etc.) |
| ip_address | TEXT | | Request source IP |
| created_at | TEXT | NOT NULL, DEFAULT | ISO 8601 timestamp |

**Indexes:**
- `idx_audit_resource` on `(resource_type, resource_id)`
- `idx_audit_actor` on `actor_id`

**Standard Actions:**
| Action | Actor Type | Details |
|---|---|---|
| `resource.created` | `user` | `{field1, field2}` |
| `resource.updated` | `user` | `{changed_fields, before, after}` |
| `resource.deleted` | `user` | `{snapshot}` |

---

## Atomic Operations

### [Operation Name] (single database transaction)

```
BEGIN TRANSACTION
  1. [write 1]
  2. [write 2]
  3. [write to audit_log]
COMMIT
```

If any step fails, the entire transaction rolls back.

---

## Migration Files

| File | Contents |
|---|---|
| `migrations/001_initial.sql` | [tables created] |

**Adding new tables:** Create a new numbered migration file. Never modify existing migration files once applied.
