---
name: next
description: Decide and run the next phase based on current state
---

# /superloopflow:next

The loop controller. Reads state, decides next phase, runs the corresponding /superloopflow:* command.

## Algorithm

1. Read state (from `.loop-state.json` or filesystem inference)
2. Apply decision tree (Phase 0 → 0.5 → 1 → 2 → 3 → 4A → 4B → 4C → 4D → 4E → 4F → 5 → 6 → 7)
3. Invoke the appropriate command
4. Print the action to user

## Usage

```
/superloopflow:next
```
