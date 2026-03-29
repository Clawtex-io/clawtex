# Bootstrap Guide

## What is the Bootstrap Snippet?

The bootstrap snippet is a small block you add to your agent's `BOOTSTRAP.md` or system prompt. It runs at every session start, loading your agent's context from Clawtex.

### Recommended Snippet (with fallback)

```bash
CLAWTEX=$(curl -sf --max-time 10 \
  -H "Authorization: Bearer tkr_your_key" \
  https://clawtex.io/api/bootstrap)

if [ -z "$CLAWTEX" ]; then
  echo "WARNING: Clawtex unreachable. Loading cached context."
  CLAWTEX=$(cat ~/.clawtex/last-bootstrap.txt 2>/dev/null || echo "No cached context available. Proceed with caution.")
else
  mkdir -p ~/.clawtex
  echo "$CLAWTEX" > ~/.clawtex/last-bootstrap.txt
fi

echo "$CLAWTEX"
```

This snippet:
- Uses `curl -sf` to fail explicitly on HTTP errors (not silently)
- Sets a 10-second timeout so sessions don't hang
- On success: caches the response locally for fallback
- On failure: loads the last cached response with a warning
- If no cache exists: clear warning so the agent knows to proceed carefully

### Minimal Snippet (no fallback)

```bash
CLAWTEX=$(curl -s -H "Authorization: Bearer tkr_your_key" https://clawtex.io/api/bootstrap)
echo "$CLAWTEX"
```

This single call returns everything your agent needs: state, lessons, rules, recent events, and logging commands. However, if Clawtex is unreachable, the agent starts with no context and no warning. We recommend the fallback version above.

## Tier-Aware Responses

The `/api/bootstrap` endpoint returns different content based on your plan:

### Free Tier

```
## Clawtex Context | My Agent
## Tier: free

### Current State
(your structured state data)

### Rules
- Entity types: plural, lowercase
- Entity IDs: lowercase, hyphenated
- GET before POST: check if entity exists before creating
- State updates: POST /api/state/:type with updated data
- This is a read-focused integration. Event logging and lessons are available on Pro.
```

### Pro Tier

```
## Clawtex Context | My Agent
## Tier: pro

### Active Lessons
[L001] When: integrating with client APIs
  CONTEXT: Agent assumed API v2 without checking, causing rework when client was on v1. Happened twice across different projects.
  CORRECTION:
    1. Check the API documentation for the current version
    2. Confirm the version with the client or their docs before writing any integration code
    3. Pin the version in config so it cannot drift silently
  ENFORCEMENT: Integration checklist item - version must be confirmed before any API calls are written

### Current State
(your structured state data)

### Recent Events (last 7 days)
- [decision] project/my-app: Switched to GraphQL (2026-03-21)
- [milestone] project/my-app: Homepage approved (2026-03-20)

### Rules
(full rules including event logging)

### Event Logging
(curl commands for logging events with your API key)

### State Updates
(curl commands for updating state)

### On Session End / Compaction
(instructions for logging before context clears)
```

### Team Tier

Same as Pro, plus multi-agent attribution instructions.

## Context Checkpoints

Pro and Team bootstrap responses include layered context checkpoints to prevent data loss:

| Context Usage | Action |
|---------------|--------|
| Below 40% | Log events naturally as they happen |
| 40-59% | Pause and log all unlogged events from this session |
| 60-79% | Log all events AND update any changed state |
| 80%+ | Log everything, update all state, prepare for session end |

These checkpoints ensure events are saved progressively throughout a session, rather than waiting until the end (where they might be lost if the session crashes or hits the context limit).

## Upgrading

When you upgrade your plan:

1. Your tier changes immediately in Clawtex
2. The dashboard shows a banner: "Start a new session to activate"
3. Start a fresh session with `/new` or `/reset`
4. Your agent calls `/api/bootstrap` and receives the expanded response
5. The banner disappears once the agent has connected

You never need to change your bootstrap snippet. The same endpoint adapts automatically.

## Replacing MEMORY.md

Clawtex replaces `MEMORY.md` and daily logs. If you currently use these:

1. Rename `MEMORY.md` to `MEMORY.archived.md`
2. Add the Clawtex bootstrap snippet to your `BOOTSTRAP.md`
3. Your agent now uses Clawtex for persistent context instead of flat files

Running both systems simultaneously will cause duplicate context in your agent's session.

## Pro Users: Import Your History

If you have existing `MEMORY.md` and daily log files, import them into Clawtex:

1. Go to **Dashboard > Import** (or **Settings > Data Import**)
2. Drag and drop your files
3. Clawtex extracts entities and events from your files
4. Review and approve the proposed data
5. Your dashboard is populated from day one
