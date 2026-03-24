# Clawtex

> Structured memory for AI agents. Your agent remembers everything.

**[clawtex.io](https://clawtex.io)** | **[Documentation](docs/)** | **[Getting Started](docs/getting-started.md)**

---

## The Problem

Every AI agent session starts from zero. Context gets lost. Decisions get repeated. Lessons never stick.

## The Fix

Clawtex gives your [OpenClaw](https://openclaw.ai) agent three layers of persistent memory:

- **State** — What exists right now. Clients, projects, tools, repos. Structured, queryable, always up to date.
- **Events** — What happened. Decisions, milestones, blockers, tasks. Timestamped, typed, linked to entities.
- **Lessons** — What to do differently. Patterns extracted from past mistakes, injected at every session start.

## Quick Start

1. Sign up at [clawtex.io](https://clawtex.io)
2. Create an agent and copy your snippet
3. Add this to your agent's `BOOTSTRAP.md`:

```bash
CLAWTEX=$(curl -s -H "Authorization: Bearer YOUR_API_KEY" https://clawtex.io/api/bootstrap)
echo "$CLAWTEX"
```

That's it. Your agent now has persistent memory that adapts to your plan.

## How It Works

```
Session starts
    ↓
Agent calls /api/bootstrap
    ↓
Clawtex returns: state + lessons + rules + recent events
    ↓
Agent works with full context
    ↓
Agent logs events as they happen
    ↓
Weekly: Clawtex extracts lessons from event patterns
    ↓
Next session: agent starts smarter than last time
```

## Plans

| Feature | Free | Pro (£9/mo) | Team (£24/mo) |
|---------|------|-------------|---------------|
| Agents | 1 | 3 | Unlimited |
| State store | ✓ | ✓ | ✓ |
| Event history | 7 days | Unlimited | Unlimited |
| Dashboard | Read-only | Full access | Full access |
| Automatic event logging | — | ✓ | ✓ |
| AI lesson extraction | — | ✓ | ✓ |
| Data import | — | ✓ | ✓ |
| Support | Email | Email (24h) | Priority (4h) |

## One Snippet. Every Tier.

The bootstrap snippet is the same for all plans. Clawtex adapts the response based on your tier:

- **Free:** Returns state and read-only rules
- **Pro:** Returns state, lessons, recent events, event logging commands, and context checkpoints
- **Team:** Everything in Pro, plus multi-agent attribution

When you upgrade, your agent gets the expanded response on its next session. No snippet changes needed.

## Documentation

- **[Getting Started](docs/getting-started.md)** — Sign up, connect your agent, first session
- **[API Reference](docs/api-reference.md)** — All endpoints, authentication, request/response formats
- **[Bootstrap Guide](docs/bootstrap-guide.md)** — How the universal snippet works, tier responses, checkpoints

## Built for OpenClaw

Clawtex is designed specifically for [OpenClaw](https://openclaw.ai) agents. It integrates through your agent's `BOOTSTRAP.md` with a single API call at session start.

## Links

- **Website:** [clawtex.io](https://clawtex.io)
- **Support:** info@clawtex.io
- **OpenClaw:** [openclaw.ai](https://openclaw.ai)
