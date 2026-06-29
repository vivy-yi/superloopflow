---
name: run
description: Bridge to OMC autopilot for full autonomous execution (idea → working code)
---

# /superloopflow:run

Bridge to **OMC `autopilot`** — full autonomous execution from idea to working code.

## What it does

1. Read `.loop-state/capability-registry.yaml` (built by `/superloopflow:discover`)
2. Find OMC's `autopilot` skill/provider
3. Execute the 5-phase autonomous pipeline:
   - **Phase 0**: Expansion — turn idea into detailed spec
   - **Phase 1**: Planning — create implementation plan
   - **Phase 2**: Execution — implement via Ralph + Ultrawork
   - **Phase 3**: QA — cycle until tests pass (up to 5 cycles)
   - **Phase 4**: Validation — multi-perspective review (Architect + Security + Code)
   - **Phase 5**: Cleanup — delete state files on success

## NO writes to third-party plugins

This command does NOT modify any file in `~/.claude/plugins/`. It only:
- Invokes OMC's CLI tool (no modification)
- Writes to `.omc/autopilot/` in YOUR project
- Uses parallel sub-agents for execution

## Bridge target

- **Provider**: OMC (oh-my-claudecode)
- **Skill**: `autopilot`
- **CLI**: `omc autopilot` or `/oh-my-claudecode:autopilot`

## Usage

```
/superloopflow:run "build me a REST API for a bookstore inventory with CRUD"
/superloopflow:run "create a CLI tool that tracks daily habits with streak counting"
```

## Pipeline flow

```
User Brief → Phase 0 (Expansion) → Phase 1 (Planning) → Phase 2 (Execution)
    ↓                                                        ↓
Phase 5 (Cleanup) ← Phase 4 (Validation) ← Phase 3 (QA Cycles)
```

## Integration with ultragoal

For large multi-goal projects, use `/superloopflow:goal` first to create the goal ledger, then invoke `/superloopflow:run` for execution. The two commands are complementary:
- `/superloopflow:goal` — durable goal tracking across sessions
- `/superloopflow:run` — autonomous execution within a session