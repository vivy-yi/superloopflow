---
name: harness
description: Bridge to ECC autonomous-agent-harness for persistent autonomous operation
---

# /superloopflow:harness

Bridge to **ECC `autonomous-agent-harness`** — persistent, self-directing agent system.

## What it does

1. Read `.loop-state/capability-registry.yaml` (built by `/superloopflow:discover`)
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
/superloopflow:harness setup       # Initialize harness components
/superloopflow:harness memory      # Set up persistent memory system
/superloopflow:harness cron        # Create a scheduled task
/superloopflow:harness dispatch    # Set up event-driven dispatch
/superloopflow:harness status      # Show current harness state
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

- `/superloopflow:goal` — set goals that survive session restarts
- `/superloopflow:run` — autonomous execution of tasks
- `/superloopflow:harness` — persistent operation and scheduling

Together they form a complete Loop Engineering system:
- **Goal** defines what to achieve
- **Run** executes autonomously
- **Harness** keeps the loop running on schedule