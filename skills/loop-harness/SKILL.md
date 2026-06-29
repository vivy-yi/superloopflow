---
name: loop-harness
description: How to use /superloopflow:harness to bridge to ECC autonomous-agent-harness for persistent autonomous operation
---

# Loop Harness (v0.5)

Bridge to **ECC `autonomous-agent-harness`** — persistent, self-directing agent system.

## Overview

`/superloopflow:harness` turns Claude Code into a persistent, self-directing agent using native features:
- Crons for scheduled tasks
- Dispatch for remote agents
- Memory for cross-session persistence
- Computer use for physical world interaction
- Task queue for surviving session boundaries

## Architecture

```
┌──────────────────────────────────────────────────────────────┐
│                    Claude Code Runtime                        │
│                                                              │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌─────────────┐ │
│  │  Crons   │  │ Dispatch │  │ Memory   │  │ Computer    │ │
│  │ Schedule │  │ Remote   │  │ Store    │  │ Use         │ │
│  └──────────┘  └──────────┘  └──────────┘  └─────────────┘ │
└──────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌──────────────────────────────────────────────────────────────┐
│              MCP Server Layer                                 │
│                                                              │
│  memory    github    exa    supabase    browser-use          │
└──────────────────────────────────────────────────────────────┘
```

## When to use

- **Continuous operation** — agent that runs 24/7
- **Scheduled tasks** — "check this every morning"
- **Event-driven workflows** — "when PR opens, do X"
- **Personal AI assistant** — remembers context across sessions
- **Replacing Hermes/AutoGPT** — native Claude Code instead

## When NOT to use

- One-off tasks — use direct commands
- You need full control — autonomous operation is designed to run unattended
- Security-sensitive operations — crons run without human oversight

## The 5 Components

### 1. Persistent Memory

**Three tiers:**

| Tier | Storage | Use Case | Persistence |
|------|---------|----------|-------------|
| Short-term | TodoWrite | Current session tasks | Session-only |
| Medium-term | `~/.claude/projects/*/memory/` | Cross-session recall | Until deleted |
| Long-term | MCP memory server | Structured knowledge graph | Permanent |

**Memory pattern:**
```
# Short-term: session context
→ TodoWrite for in-session task tracking

# Medium-term: project memory
→ Write to ~/.claude/projects/*/memory/

# Long-term: knowledge graph
→ mcp__memory__create_entities
→ mcp__memory__create_relations
→ mcp__memory__add_observations
```

### 2. Scheduled Operations (Crons)

```bash
# Via MCP tool
mcp__scheduled-tasks__create_scheduled_task({
  name: "daily-pr-review",
  schedule: "0 9 * * 1-5",  // 9 AM weekdays
  prompt: "Review all open PRs...",
  project_dir: "/path/to/repo"
})
```

**Useful patterns:**

| Schedule | Use Case |
|----------|----------|
| `0 9 * * 1-5` | Daily standup |
| `0 10 * * 1` | Weekly review |
| `0 * * * *` | Hourly monitor |
| `0 2 * * *` | Nightly build |
| `*/30 * * * *` | Pre-meeting prep |

### 3. Dispatch / Remote Agents

Event-driven workflow triggers:

```bash
# From CI/CD
curl -X POST "https://api.anthropic.com/dispatch" \
  -H "Authorization: Bearer $ANTHROPIC_API_KEY" \
  -d '{"prompt": "Build failed. Diagnose and fix.", "project": "/repo"}'

# From GitHub webhook
# webhook → dispatch → Claude agent → fix → PR
```

### 4. Computer Use

Browser and desktop automation:
- Automated UI testing
- Form filling
- Screenshot-based monitoring
- Multi-app workflows

### 5. Task Queue

Persistent queue surviving session boundaries:

```markdown
---
name: task-queue
type: project
---

## Active Tasks
- [ ] PR #123: Review if CI green
- [ ] Monitor deploy: check /health every 30 min
- [ ] Research: Find 5 leads in AI tooling

## Completed
- [x] Daily standup: reviewed 3 PRs
```

## Usage

```bash
/superloopflow:harness setup       # Initialize harness components
/superloopflow:harness memory      # Set up persistent memory system
/superloopflow:harness cron        # Create a scheduled task
/superloopflow:harness dispatch    # Set up event-driven dispatch
/superloopflow:harness status      # Show current harness state
```

## Setup MCP servers

In `~/.claude.json`:

```json
{
  "mcpServers": {
    "memory": {
      "command": "npx",
      "args": ["-y", "@anthropic/memory-mcp-server"]
    },
    "scheduled-tasks": {
      "command": "npx",
      "args": ["-y", "@anthropic/scheduled-tasks-mcp-server"]
    },
    "computer-use": {
      "command": "npx",
      "args": ["-y", "@anthropic/computer-use-mcp-server"]
    }
  }
}
```

## Integration with /superloopflow:goal and /superloopflow:run

```
┌─────────────────────────────────────────────────────────────┐
│                    Complete Loop Stack                       │
│                                                             │
│  /superloopflow:goal    — What to achieve (durable goals)            │
│  /superloopflow:run     — How to execute (autonomous execution)      │
│  /superloopflow:harness — Keep it running (scheduling + memory)      │
└─────────────────────────────────────────────────────────────┘
```

Example pipeline:
```bash
# 1. Set goals that survive restarts
/superloopflow:goal create-goals --brief "build and maintain the API"

/superloopflow:harness cron "0 9 * * 1-5" "morning standup: review PRs, check deploy status"

/superloopflow:run "build the API"  # Autonomous execution
```

## State files written

| File | Location | Purpose |
|------|----------|---------|
| Task queue | `~/.claude/projects/*/memory/task-queue.md` | Persistent task list |
| Memory entities | MCP memory server | Knowledge graph |
| Cron schedules | MCP scheduled-tasks | Recurring tasks |

## Constraints

- Cron tasks run in isolated sessions — no context sharing without memory
- Computer use requires explicit permission grants
- Remote dispatch may have rate limits — design crons with appropriate intervals
- Memory files should be kept concise — archive old data