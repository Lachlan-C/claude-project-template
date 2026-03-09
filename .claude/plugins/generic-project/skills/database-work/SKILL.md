---
name: database-work
description: >
  This skill should be used when the user asks to "modify SQL queries", "write a migration",
  "change data model", "update schema", "database queries", "ORM", "data access",
  "tenant scoping", "audit_log", or works with any database access code.
---

# Database Work

## Hard Rules

Follow these rules without exception when writing or modifying database code:

1. **Parameterized bindings only.** Never interpolate values into SQL strings. Use your ORM or driver's bind parameters.

2. **Scope every query to the authenticated user's context.** Every SELECT, UPDATE, and DELETE must be scoped to the authenticated user's tenant/org. No exceptions.

3. **Treat audit_log as append-only.** Never issue UPDATE or DELETE against the audit log. Only INSERT.

4. **Never edit an existing migration file.** To change the schema, create a new sequentially numbered migration file. Treat all prior migrations as sealed.

## Schema as Source of Truth

Consult `docs/schema.md` as the canonical source before making any schema change. Never let the code diverge from the documented schema without updating the doc first.

## Atomic Operations

Multi-step writes (e.g. create + audit log + status update) must happen in a single transaction. If any step fails, the entire operation rolls back.

## Checklist Before Submitting Database Changes

- [ ] Every new or modified query is scoped to the authenticated user's context.
- [ ] No string interpolation in any SQL statement.
- [ ] Audit log is only ever inserted into, never updated or deleted.
- [ ] Any schema change is in a new migration file, not an edit to an existing one.
- [ ] Field names, types, and constraints match `docs/schema.md`.
