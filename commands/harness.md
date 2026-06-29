---
name: harness
description: Bridge to ECC autonomous-agent-harness for persistent autonomous operation
---

# /loop:harness

Bridge to **ECC `autonomous-agent-harness`** — persistent, self-directing agent system.

## What it does

1. Read `.loop-state/capability-registry.yaml` (built by `/loop:discover`)
2. Find ECC's `autonomous-agent-harness` skill/provider
3. Set up key components:
   - **Persistent Memory** — Claude memory + MCP memory server for cross-session recall
   - **Scheduled Operations** — Cron-based recurring agent tasks
   - **Dispatch/Remote Agents** — Event-driven workflows via webhooks or CI
   - **Computer Use** — Browser and desktop automation
   - **Task Queue** — Persistent queue surviving session boundaries

## NO writes to third-party plugins

This command does NOT modify any file in `~/.claude/plugins/`. It only:
- Reads plugin capabilities (read-only discovery)
- Writes to your project's memory and schedule files
- Configures MCP servers in your `~/.claude.json`

## Bridge target

- **Provider**: ECC (everything-claude-code)
- **Skill**: `autonomous-agent-harness`
- **Features**: crons, dispatch, memory store, computer use, task queue

## Usage

```
/loop:harness setup       # Initialize harness components
/loop:harness memory      # Set up persistent memory system
/loop:harness cron        # Create a scheduled task
/loop:harness dispatch    # Set up event-driven dispatch
/loop:harness status      # Show current harness state
```

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
```

## Integration with other LOOP commands

- `/loop:goal` — set goals that survive session restarts
- `/loop:run` — autonomous execution of tasks
- `/loop:harness` — persistent operation and scheduling

Together they form a complete Loop Engineering system:
- **Goal** defines what to achieve
- **Run** executes autonomously
- **Harness** keeps the loop running on schedule