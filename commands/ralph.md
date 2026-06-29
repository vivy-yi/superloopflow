---
name: ralph
description: Bridge to OMC /omc:ralph for persistent fix loop — fix failing tests until green
---

# /loop:ralph

Bridge to **OMC `/omc:ralph`** — persistent fix loop that iterates until tests pass or max iterations reached.

## What it does

1. Read `.loop-state/capability-registry.yaml`
2. Find OMC's `ralph` capability
3. Invoke `/omc:ralph "fix failing tests until green"`
4. Ralph mode = persistent mode with verify/fix loops
5. Iterates until tests pass OR max-iteration cap reached

## When to use

- Tests are failing and you hit a wall
- Same error persists across multiple fix attempts
- You want autonomous debugging without manual intervention

## When NOT to use

- Fundamental issue requiring architectural change (use `systematic-debugging` skill)
- You're not sure if the test is correct (verify test first)

## Bridge target

- **Provider**: OMC (oh-my-claudecode)
- **Command**: `/omc:ralph`
- **Purpose**: Persistent fix loop

## Usage

```bash
/loop:ralph "fix failing tests until green"
/loop:ralph "fix the authentication bug"
/loop:ralph "resolve all lint errors"
```

## Exit states

| State | Meaning |
|-------|---------|
| `tests_passing = true` | Ralph successfully fixed all issues |
| `max_iterations_reached` | Could not fix in allowed iterations — escalate |
| `fundamental_issue_detected` | Same error 3x — requires human intervention |

## After ralph

If ralph hits max iterations:
1. Use `systematic-debugging` skill manually
2. Verify the test is correct (not the implementation)
3. Consider whether the spec was wrong

## Integration with /loop:implement

```
/loop:implement "build the feature"
  ↓ (tests fail)
/loop:ralph "fix failing tests"
  ↓ (ralph hits wall)
/loop:review
  ↓ (review finds issues)
/loop:ralph "fix review findings"
```