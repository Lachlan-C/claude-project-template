# API Reference

> **Related files:** [schema.md](schema.md) (tables & fields) | [design.md](design.md) (system design) | [../constitution.md](../constitution.md) (immutable rules)

This document is the single source of truth for all HTTP endpoints, request/response payloads, authentication requirements, and external API interactions. **Read this before adding or modifying any endpoint.**

> **This doc owns:** All endpoint URLs, request/response shapes, HTTP status codes, auth requirements, rate limits, and external API contracts. Other docs ([spec.md](spec.md), [tasks.md](tasks.md)) reference endpoints here by link — they never repeat the URL or payload inline.

---

## Base URL

```
Development: http://localhost:[PORT]
```

## API Versioning

<!-- TODO: describe versioning strategy -->
| Prefix | Scope |
|---|---|
| `/v1/api/...` | Programmatic API (versioned) |
| `/webhooks/...` | Webhooks (unversioned — external contract) |
| `/...` | UI pages (unversioned — internal only) |

## Authentication Methods

| Method | Header/Cookie | Used By | Protects |
|---|---|---|---|
| <!-- TODO --> | | | |

---

## Endpoints

### Health Check

```
GET /health
```

**Auth:** None
**Response:** `200 OK`
```json
{ "status": "ok" }
```

---

### [Resource] API

#### Create [Resource]

```
POST /v1/api/[resource]
```

**Auth:** [method]
**Content-Type:** `application/json`

**Request:**
```json
{
  "field": "value"
}
```

| Field | Type | Required | Notes |
|---|---|---|---|
| field | string | yes | description |

**Response: `201 Created`**
```json
{
  "id": "...",
  "field": "value",
  "created_at": "2024-01-01T00:00:00Z"
}
```

**Errors:**
| Status | Body | Cause |
|---|---|---|
| 400 | `{"error": "..."}` | Validation failure |
| 401 | `{"error": "..."}` | Missing/invalid auth |
| 422 | `{"error": "..."}` | Valid input but unprocessable |

**Side effects:**
- Creates row in `[table]`
- Writes `audit_log` entry: `[resource].created`

---

#### Get [Resource]

```
GET /v1/api/[resource]/:id
```

**Auth:** [method]

**Response: `200 OK`**
```json
{
  "id": "...",
  "field": "value"
}
```

**Errors:**
| Status | Cause |
|---|---|
| 404 | Resource not found |

---

#### Update [Resource]

```
PUT /v1/api/[resource]/:id
```

**Auth:** [method]
**Content-Type:** `application/json`

**Request:**
```json
{
  "field": "new_value"
}
```

**Response: `200 OK`** — Updated object

**Side effects:**
- Writes `audit_log` entry: `[resource].updated` with before/after values

---

#### Delete [Resource]

```
DELETE /v1/api/[resource]/:id
```

**Auth:** [method]
**Response: `204 No Content`**

**Side effects:**
- Writes `audit_log` entry: `[resource].deleted`

---

### Webhook Endpoint (if applicable)

#### [Service] Webhook Receiver

```
POST /webhooks/[service]
```

**Auth:** Shared secret via `Authorization` header. Verify with constant-time string comparison **before any processing**.

**Processing order (strict):**
1. Verify `Authorization` header — reject if invalid (401)
2. Parse JSON payload
3. Process event

**Response:**
| Status | Meaning |
|---|---|
| 200 | Webhook processed |
| 401 | Invalid auth — **returned before any parsing** |
| 429 | Rate limited |

---

## Error Response Format

All error responses use a consistent JSON format:

```json
{
  "error": "human-readable error message"
}
```

**HTTP status code conventions:**
| Status | Meaning |
|---|---|
| 400 | Bad request — invalid input, validation failure |
| 401 | Unauthorized — missing or invalid auth credentials |
| 403 | Forbidden — valid auth but insufficient permissions |
| 404 | Not found — resource doesn't exist |
| 413 | Payload too large |
| 422 | Unprocessable — valid input but can't process |
| 429 | Rate limited — try again later |
| 500 | Internal server error — unexpected failure |

---

## Rate Limits

| Scope | Limit | Applies To |
|---|---|---|
| General | 100 req/min per IP | All endpoints |
| Auth | 10 req/min per IP | Login, register |

Rate limit headers returned on every response:
```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: [unix timestamp]
```
