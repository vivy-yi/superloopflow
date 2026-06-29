---
name: create-skills
description: Create meta skills + skills/INDEX.md
---

# /superloopflow:create-skills

Implements Phase 4D of LOOP.md — the project skills.

## Steps

1. For each meta skill, copy from `templates/skills/meta-*.md`:
   - `meta-reviewer.md` (self-review protocol)
   - `meta-checkpoint-protocol.md` (checkpoint protocol)
   - `meta-onboarding.md` (first-interaction)
   - `meta-skill-creator.md` (how to create skills)
   - `meta-capability-extension.md` (how to add tools/pipelines)
2. Generate `templates/skills/INDEX.md` referencing all skills
3. Commit: `chore(meta): add project skills and INDEX`

## Output

5 meta skills + 1 INDEX under `skills/`.

## Usage

```
/superloopflow:create-skills
```
