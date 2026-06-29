---
name: agent-registry-author
description: How to author agent roles in agents/registry.yaml and agents/<name>.md. Use when defining new agent roles for the project.
---

# Agent Registry Author

## When to Use
- Identifying needed agent roles (brainstorming session)
- Writing agents/registry.yaml
- Authoring agents/<role>.md

## Process
1. Identify roles via `/omc:deep-interview`
2. For each role, define:
   - name (kebab-case)
   - purpose
   - input/output contract
   - required skills
   - required tools
   - success criteria
3. Write registry entry
4. Write per-role document with process + self-evaluation
5. Add to INDEX.md

## Self-Evaluation
- [ ] Role is single-purpose?
- [ ] Contract is clear?
- [ ] Skills are real (not invented)?
- [ ] Tools are real (in BaseTool registry)?
