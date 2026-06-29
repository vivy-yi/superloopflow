---
name: meta-skill-creator
description: How to create a new skill for the project. Use when adding a new L2 skill to skills/.
---

# Meta Skill Creator

## When to Use
- Adding a new skill to `skills/core/`, `skills/meta/`, or `skills/pipelines/<pipeline>/`
- The same workflow keeps coming up and is worth codifying
- A new pipeline stage needs a director skill

## Process

1. **Identify the skill's scope:**
   - **core/**: cross-cutting project-wide conventions (e.g., TDD workflow)
   - **meta/**: process skills (e.g., reviewer, checkpoint)
   - **pipelines/<pipeline>/<stage>-director.md**: per-stage execution
2. **Pick a kebab-case name** (e.g., `image-selector`, `checkpoint-protocol`).
3. **Choose the right template:**
   - core/ → start from an existing core skill
   - meta/ → start from another meta skill (e.g., `meta-reviewer.md`)
   - pipelines/ → start from an existing stage director
4. **Author the SKILL.md body:**
   - **Frontmatter:** `name` + `description` (description is the trigger — be specific)
   - **When to Use** section (trigger conditions)
   - **Process** section (numbered steps)
   - **Output format** (if applicable)
   - **Self-Evaluation** checklist
5. **Wire it up:**
   - Add the skill to `skills/INDEX.md` in the right table
   - If it's a stage director: reference it from `pipeline_defs/<pipeline>.yaml` > `stages[].skill`
6. **Test it:** invoke the skill manually on a real task; verify the output matches the spec.
7. **Commit:** `chore(skills): add <skill-name>` (or `feat(skills):` if it's a major capability)

## Self-Evaluation
- [ ] Is the description specific enough to be a useful trigger?
- [ ] Is the process a numbered sequence (not vague prose)?
- [ ] Is the output format concrete (template, not description)?
- [ ] Is the self-evaluation checklist actually checkable?
- [ ] Is the skill referenced from INDEX.md and any relevant manifest?
