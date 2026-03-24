# Getting Started

## 1. Create an Account

Go to [clawtex.io](https://clawtex.io) and sign up. Free plan gets you started immediately.

## 2. Create an Agent

During onboarding, name your agent. This creates an API key and generates your bootstrap snippet.

## 3. Add the Snippet

Copy the snippet into your agent's `BOOTSTRAP.md` or system prompt:

```bash
## Clawtex - Structured Memory for My Agent
# Loads your agent's context at session start. Content adapts to your plan.

CLAWTEX=$(curl -s -H "Authorization: Bearer tkr_your_api_key" https://clawtex.io/api/bootstrap)
echo "$CLAWTEX"

# Follow the instructions returned above. Clawtex replaces MEMORY.md and daily logs.
# Rename MEMORY.md to MEMORY.archived.md in your workspace.
```

## 4. Start a New Session

Start a fresh session with your agent (type `/new` or `/reset` in your agent's chat). The agent will call the Clawtex API and load your context.

## 5. Verify Connection

The Clawtex dashboard shows your agent's connection status. Once the agent makes its first API call, the status changes from "Setup required" to "Active".

## What Happens Next

**Free plan:**
- Your agent loads state at session start
- Tell your agent to log events by prompting it directly
- State grows as your agent works

**Pro plan:**
- Your agent loads state, lessons, and recent events at session start
- Events are logged automatically (the bootstrap includes logging commands)
- Lessons are extracted weekly from your event history
- Import your existing MEMORY.md and daily logs from the dashboard

## Important Notes

- **Clawtex replaces MEMORY.md.** Rename your existing `MEMORY.md` to `MEMORY.archived.md` to avoid duplicate context.
- **Fresh sessions required.** Your agent reads the bootstrap at session start. Changes (like upgrading your plan) take effect on the next session.
- **One snippet for all plans.** The API returns different content based on your tier. When you upgrade, your agent automatically gets the expanded response.

## Need Help?

- Use the **Contact Support** button in your dashboard (all tiers)
- Email: info@clawtex.io
