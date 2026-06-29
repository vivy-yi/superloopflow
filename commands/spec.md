---
name: spec
description: Bridge to ANY plugin providing spec authoring (v0.4 — read-only)
---

# /superloopflow:spec (v0.4)

Read-only bridge to any plugin providing spec capability.

## What it does

1. Read `.loop-state/capability-registry.yaml` (built by `/superloopflow:discover`)
2. If not built yet, do read-only discovery first
3. Find providers of "spec" capability
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
- `planning-with-files` `doc-coauthoring` skill
- `superpowers` spec-related skills

Other providers added later will be auto-detected.

## Usage

```
/superloopflow:spec
```
