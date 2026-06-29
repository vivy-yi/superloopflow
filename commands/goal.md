---
name: goal
description: Bridge to OMC ultragoal for durable multi-goal workflow with ledger
---

# /loop:goal

Bridge to **OMC `ultragoal`** — durable multi-goal workflow with persistent ledger.

## What it does

1. Read `.loop-state/capability-registry.yaml` (built by `/loop:discover`)
2. Find OMC's `ultragoal` skill/provider
3. Execute `omc ultragoal` commands:
   - `create-goals` — create goals from brief or explicit list
   - `complete-goals` — start/resume the next story
   - `checkpoint` — record goal completion with evidence
   - `status` — inspect current state
   - `record-review-blockers` — append blocker stories

## NO writes to third-party plugins

This command does NOT modify any file in `~/.claude/plugins/`. It only:
- Invokes OMC's CLI tool (no modification)
- Writes to `.omc/ultragoal/` in YOUR project
- Uses Claude Code `/goal` command as the in-session execution primitive

## Bridge target

- **Provider**: OMC (oh-my-claudecode)
- **Skill**: `ultragoal`
- **CLI**: `omc ultragoal`

## Usage

```
/loop:goal create-goals --brief-file plan.md
/loop:goal create-goals --brief "ship the migration" --goal "Schema::Add columns" --goal "Backfill::Backfill rows" --goal "Cutover::Switch reads"
/loop:goal complete-goals
/loop:goal status
/loop:goal checkpoint --goal-id G001-... --status complete --evidence "tests passed"
/loop:goal record-review-blockers --goal-id G00X-... --title "Fix review findings" --evidence "<review findings>"
```

## How it works

```
Brief → Goals (ledger) → Claude /goal (session) → ultragoal checkpoint → Next goal
```

Each goal survives session restarts and worktree switches. The ledger is append-only and durable.