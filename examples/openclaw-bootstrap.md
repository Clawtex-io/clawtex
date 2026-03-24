# Example: OpenClaw Bootstrap Integration

Add this to your agent's `BOOTSTRAP.md`:

```markdown
## Clawtex - Structured Memory
# Loads context at session start. Content adapts to your plan.

CLAWTEX=$(curl -s -H "Authorization: Bearer tkr_your_api_key" https://clawtex.io/api/bootstrap)
echo "$CLAWTEX"

# Follow the instructions above. Clawtex replaces MEMORY.md and daily logs.
# Rename MEMORY.md to MEMORY.archived.md in your workspace.
```

## What Your Agent Receives

On each session start, the Clawtex API returns a formatted text block containing:

- **State** — all your structured entities (clients, projects, tools, etc.)
- **Lessons** (Pro/Team) — patterns extracted from past mistakes
- **Recent events** (Pro/Team) — what happened in the last 7 days
- **Rules** — entity naming conventions and logging instructions
- **Logging commands** (Pro/Team) — curl commands with your API key for logging events and updating state

Your agent reads this block and uses it as context for the session.

## Sign Up

Get your API key at [clawtex.io](https://clawtex.io).
