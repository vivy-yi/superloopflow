---
name: status
description: Show the current LOOP state (read .loop-state.json or infer from filesystem)
---

# /superloopflow:status

Read `.loop-state.json` if it exists, otherwise infer state from filesystem reality.

## Output

- Current phase
- Completed phases
- Next recommended action
- Open file count

## Usage

```
/superloopflow:status
```
