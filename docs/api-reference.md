# API Reference

Base URL: `https://clawtex.io/api`

## Authentication

All requests require a Bearer token in the Authorization header:

```
Authorization: Bearer tkr_your_api_key
```

API keys are generated when you create an agent in the Clawtex dashboard. You can reveal, copy, or regenerate keys from the Agents page.

---

## Endpoints

### GET /api/bootstrap

The universal context endpoint. Returns a plain text block with everything your agent needs for the current session. Content adapts to your plan tier.

**Free tier response:** State data and read-only rules.

**Pro/Team tier response:** Lessons, state data, recent events (7 days), event logging commands with context checkpoints, and state update commands.

```bash
curl -s -H "Authorization: Bearer tkr_..." https://clawtex.io/api/bootstrap
```

**Response:** `text/plain` - designed to be read directly by your agent.

---

### GET /api/state

Returns all state entities for the authenticated user.

```bash
curl -s -H "Authorization: Bearer tkr_..." https://clawtex.io/api/state
```

**Response:** JSON array of entities grouped by type.

---

### GET /api/state/:type

Returns all entities of a specific type.

```bash
curl -s -H "Authorization: Bearer tkr_..." https://clawtex.io/api/state/clients
```

---

### POST /api/state/:type

Create or update a state entity.

```bash
curl -s -X POST https://clawtex.io/api/state/clients \
  -H "Authorization: Bearer tkr_..." \
  -H "Content-Type: application/json" \
  -d '{
    "id": "acme-corp",
    "data": {
      "name": "Acme Corp",
      "status": "active",
      "contact": "jane@acme.com"
    }
  }'
```

**Fields:**
- `id` (required) - Unique identifier within the type. Lowercase, hyphenated.
- `data` (required) - Any JSON object. No predefined schema.

Types are created automatically the first time you write to them.

---

### GET /api/events

Returns events for the authenticated user.

**Query parameters:**
- `days` - Number of days to look back (default: 7, free tier max: 14)
- `type` - Filter by event type
- `limit` - Maximum events to return (default: 50)
- `offset` - Pagination offset

```bash
curl -s -H "Authorization: Bearer tkr_..." "https://clawtex.io/api/events?days=7&limit=50"
```

---

### POST /api/events

Log a new event.

```bash
curl -s -X POST https://clawtex.io/api/events \
  -H "Authorization: Bearer tkr_..." \
  -H "Content-Type: application/json" \
  -d '{
    "type": "decision",
    "entity": "project/my-app",
    "summary": "Switched from REST to GraphQL after performance testing"
  }'
```

**Fields:**
- `type` (required) - One of: `decision`, `milestone`, `blocker`, `task`, `note`, `deploy`, `contact`
- `summary` (required) - One sentence description
- `entity` (optional) - Entity reference in `type/id` format (e.g. `project/my-app`, `client/acme-corp`)
- `date` (optional) - ISO date string. Defaults to now.
- `time` (optional) - HH:MM format. Defaults to now.
- `agent` (optional) - Agent name for multi-agent attribution (Team tier).

---

### GET /api/lessons/inject

Returns the lesson injection block for the authenticated user. Pro/Team only.

```bash
curl -s -H "Authorization: Bearer tkr_..." https://clawtex.io/api/lessons/inject
```

**Note:** The `/api/bootstrap` endpoint already includes lessons. This endpoint is available for custom integrations that need lessons separately.

---

### POST /api/lessons/extract

Trigger a lesson extraction from recent event history. Pro/Team only.

```bash
curl -s -X POST -H "Authorization: Bearer tkr_..." https://clawtex.io/api/lessons/extract
```

**Rate limit:** 5 extractions per day (weekly mode). Initial extraction (no existing lessons) is unlimited.

**Response:** JSON with proposed lessons in v2 format. Each lesson includes:
- `context` - the trigger situation
- `mistake` - background and what went wrong, with event references
- `correct` - numbered correction steps (verifiable process)
- `enforcement` - how the lesson is mechanically enforced
- `source_events` - event references the lesson was extracted from

---

## Rate Limits

| Tier | Daily API calls | Extraction limit |
|------|----------------|-----------------|
| Free | 50 | N/A (lessons require Pro) |
| Pro | 500 | 5 per day |
| Team | 10,000 | 5 per day |

---

## Entity Conventions

- **Entity types:** Plural, lowercase (e.g. `clients`, `projects`, `repos`)
- **Entity IDs:** Lowercase, hyphenated (e.g. `acme-corp`, `website-rebuild`)
- **Entity references:** `type/id` format (e.g. `project/website-rebuild`)
- **Check before creating:** GET the entity first to avoid duplicates

---

## Errors

All errors return JSON with an `error` field:

```json
{
  "error": "unauthorized"
}
```

Common status codes:
- `401` - Invalid or missing API key
- `403` - Feature requires a higher tier
- `500` - Server error (try again)
