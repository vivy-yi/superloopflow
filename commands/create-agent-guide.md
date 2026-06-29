---
name: create-agent-guide
description: Generate AGENT_GUIDE.md from template (interactively customized)
---

# /loop:create-agent-guide

Implements Phase 4B of LOOP.md — the main agent contract.

## Steps

1. Read project state (`.loop-state.json` or interactive)
2. Ask user 5 key questions (interactive):
   - What's the project name?
   - What's the one-sentence identity?
   - What's the tech stack (LLM provider, framework)?
   - What's the canonical tool path?
   - What are the anti-patterns to flag?
3. Substitute answers into `templates/agent-guide.md`
4. Write `AGENT_GUIDE.md` (700-1000 lines after substitution)
5. Commit: `docs(meta): add AGENT_GUIDE.md (main agent contract)`

## Output

`AGENT_GUIDE.md` at project root.

## Usage

```
/loop:create-agent-guide
```
