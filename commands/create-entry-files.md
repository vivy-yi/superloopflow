---
name: create-entry-files
description: Create 5 zero-content entry point files (CLAUDE.md, AGENTS.md, etc.)
---

# /loop:create-entry-files

Implements Phase 4A of LOOP.md — the 5 entry files for AI tools.

## Steps

1. For each of `CLAUDE.md`, `AGENTS.md`, `CODEX.md`, `CURSOR.md`, `COPILOT.md`:
   - Check if file exists (skip if so)
   - Copy from `templates/entry-files/<name>.md`
   - Each file: 8 lines, all pointing to `AGENT_GUIDE.md`
2. Commit: `chore(meta): add 5 entry point files for AI tools`

## Templates used

`templates/entry-files/CLAUDE.md` (and 4 others)

## Output

5 files at project root, all 8 lines, all pointing to `AGENT_GUIDE.md`.

## Usage

```
/loop:create-entry-files
```
