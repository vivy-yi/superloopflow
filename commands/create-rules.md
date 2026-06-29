---
name: create-rules
description: Create 4 common rules + 1 python rule under .claude/rules/
---

# /loop:create-rules

Implements Phase 4C of LOOP.md — the project rules.

## Steps

1. Detect primary language (Python / TypeScript / Go / etc.)
2. For each common rule, copy from `templates/rules/common-*.md`:
   - `common-coding-style.md` (immutability, file org)
   - `common-git-workflow.md` (commits, branches)
   - `common-testing.md` (TDD, coverage)
   - `common-performance.md` (model selection, context)
3. For language-specific rule, copy from `templates/rules/python-style.md` (or appropriate)
4. Commit: `chore(meta): add project rules`

## Output

5 rule files under `.claude/rules/`.

## Usage

```
/loop:create-rules
```
