---
name: create-agent-registry
description: Create agents/registry.yaml and per-agent <name>.md role documents
---

# /superloopflow:create-agent-registry

Implements Phase 4F of LOOP.md — agent registry.

## Steps

1. Read project spec + pipeline manifest to extract needed agent roles
2. For each role, ask user 4 questions (interactive):
   - Role name (kebab-case)
   - Purpose (one sentence)
   - Input/output contract
   - Required skills and tools
3. Generate `agents/registry.yaml` from `templates/agent-registry.yaml`
4. For each role, generate `agents/<role>.md` from a per-role template
5. Commit: `chore(agents): add agent registry and role documents`

## Output

`agents/registry.yaml` + N `agents/<role>.md` files.

## Usage

```
/superloopflow:create-agent-registry
```
