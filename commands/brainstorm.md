---
name: brainstorm
description: Bridge to ANY plugin providing brainstorming (v0.4 — read-only)
---

# /loop:brainstorm (v0.4)

Read-only bridge to any plugin providing brainstorming capability.

## What it does

1. Read `.loop-state/capability-registry.yaml` (built by `/loop:discover`)
2. If not built yet, do read-only discovery first
3. Find providers of "brainstorming" capability
4. If multiple, ask user to pick. If one, use it.
5. Load LOOP context
6. Invoke provider's command or skill
7. Capture output
8. Update state

## NO writes to third-party plugins

This command does NOT modify any file in `~/.claude/plugins/`. It only:
- Reads `~/.claude/settings.json` (your config)
- Reads plugin directories (read-only)
- Writes to `.loop-state.json` in YOUR project
- Invokes existing plugin commands (no modification)

## Bridge target

Currently identifies (in priority order):
- `superpowers` `brainstorming` skill
- `omc` `/omc:deep-interview` command

Other providers added later will be auto-detected.

## Usage

```
/loop:brainstorm
```
