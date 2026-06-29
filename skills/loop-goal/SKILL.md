---
name: loop-goal
description: How to use /superloopflow:goal to bridge to OMC ultragoal for durable multi-goal workflow with ledger
---

# Loop Goal (v0.5)

Bridge to **OMC `ultragoal`** — durable multi-goal workflow with persistent ledger.

## Overview

`/superloopflow:goal` provides goal tracking that **survives session restarts**. Unlike Claude Code's `/goal` which is session-scoped, ultragoal's ledger persists across:
- Session restarts
- Worktree switches
- Fresh clones

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     Your Project                             │
│  .omc/ultragoal/plans/{planId}/                              │
│    ├── goals.md          (ordered goal list)                │
│    ├── ledger.jsonl      (append-only event log)            │
│    └── checkpoints/      (evidence snapshots)               │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│              OMC ultragoal CLI + Claude /goal                │
│                                                              │
│  ultragoal creates artifacts → prints handoff → Claude /goal │
└─────────────────────────────────────────────────────────────┘
```

## When to use

- **Large multi-session projects** — work that takes days/weeks
- **Complex features** — broken into ordered "stories"
- **Team environments** — multiple sessions working on same project
- **Auditable progress** — ledger is append-only with evidence

## When NOT to use

- Single small change — use direct delegation
- Planning-only artifact — use `/superloopflow:plan` instead
- Quick bug fix — use `/superloopflow:implement` directly

## Workflow

### 1. Create goals from a brief

```bash
/superloopflow:goal create-goals --brief-file plan.md

# OR with explicit stories:
/superloopflow:goal create-goals --brief "ship the migration" \
  --goal "Schema::Add new columns" \
  --goal "Backfill::Backfill rows in batches" \
  --goal "Cutover::Drop old columns and switch reads"
```

### 2. Start/resume the next story

```bash
/superloopflow:goal complete-goals
```

This prints a model-facing handoff. The active Claude agent reads it and:
- Confirms/sets the active `/goal` condition
- Works the story
- Calls checkpoint when complete

### 3. Checkpoint a story

```bash
/superloopflow:goal checkpoint --goal-id G001-... --status complete \
  --evidence "tests/files/PR evidence" \
  --claude-goal-json '{"goal":{"objective":"...","status":"active"}}'
```

For the final story, also pass quality gate evidence (aiSlopCleaner + verification + codeReview all clean).

### 4. If review fails, record blockers

```bash
/superloopflow:goal record-review-blockers --goal-id G00X-... \
  --title "Resolve final code-review blockers" \
  --evidence "<review findings>"
```

### 5. Inspect state

```bash
/superloopflow:goal status
```

## Integration with /superloopflow:run

```
/superloopflow:goal create-goals --brief "build a REST API"
  ↓
/superloopflow:run "build the schema story"
  ↓
/superloopflow:goal checkpoint --goal-id G001 --status complete
  ↓
/superloopflow:run "build the backfill story"
  ↓
/superloopflow:goal checkpoint --goal-id G002 --status complete
  ...
```

## Multi-repo workspaces

When multiple Claude sessions in the same workspace need to run `/superloopflow:goal` concurrently:

```bash
/superloopflow:goal create-goals --auto-plan-id --brief "ship the feature"
```

This creates `.omc/ultragoal/plans/{epochMs-slug}/` instead of the shared path. Then thread `--plan-id <id>` through every subsequent command.

## State files written

| File | Location | Purpose |
|------|----------|---------|
| `goals.md` | `.omc/ultragoal/plans/{planId}/` | Ordered goal list |
| `ledger.jsonl` | `.omc/ultragoal/plans/{planId}/` | Append-only event log |
| `checkpoints/*.json` | `.omc/ultragoal/plans/{planId}/` | Evidence snapshots |