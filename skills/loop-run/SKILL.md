---
name: loop-run
description: How to use /loop:run to bridge to OMC autopilot for full autonomous execution (idea to working code)
---

# Loop Run (v0.5)

Bridge to **OMC `autopilot`** — full autonomous execution from idea to working code.

## Overview

`/loop:run` takes a brief product idea and autonomously handles the full lifecycle:
- Requirements analysis
- Technical design
- Planning
- Parallel implementation
- QA cycling
- Multi-perspective validation

## Architecture

```
User Brief → Phase 0 (Expansion) → Phase 1 (Planning) → Phase 2 (Execution)
    ↓                                                        ↓
Phase 5 (Cleanup) ← Phase 4 (Validation) ← Phase 3 (QA Cycles)
```

## When to use

- **End-to-end features** — "build me a CLI tool that does X"
- **Full autonomous execution** — hands-off from idea to working code
- **Multi-phase tasks** — planning + coding + testing + validation
- **When you trust the system** — autopilot runs to completion

## When NOT to use

- Exploration/brainstorming — use `/loop:brainstorm` or `/loop:plan`
- Single focused fix — use direct delegation
- You want to review each step — use `/loop:plan` + `/loop:implement` instead

## The 5 Phases

### Phase 0: Expansion

Turn vague idea into detailed spec:
- Analyst (Opus) extracts requirements
- Architect (Opus) creates technical specification
- Output: `.omc/autopilot/spec.md`

### Phase 1: Planning

Create implementation plan:
- Architect (Opus) creates plan
- Critic (Opus) validates plan
- Output: `.omc/plans/autopilot-impl.md`

### Phase 2: Execution

Implement the plan using Ralph + Ultrawork:
- Executor (Haiku): Simple tasks
- Executor (Sonnet): Standard tasks
- Executor (Opus): Complex tasks
- Independent tasks run in parallel

### Phase 3: QA

Cycle until all tests pass (up to 5 cycles):
- Build, lint, test, fix failures
- If same error repeats 3×, stop and report fundamental issue

### Phase 4: Validation

Multi-perspective review in parallel:
- Architect: Functional completeness
- Security-reviewer: Vulnerability check
- Code-reviewer: Quality review
- All must approve; fix and re-validate on rejection

### Phase 5: Cleanup

Delete all state files on success:
- `.omc/state/autopilot-state.json`
- `ralph-state.json`, `ultrawork-state.json`, `ultraqa-state.json`

## Usage

```bash
/loop:run "build me a REST API for a bookstore inventory with CRUD"
/loop:run "create a CLI tool that tracks daily habits with streak counting"
/loop:run "build me something with authentication and rate limiting"
```

## Better input = better output

| Good | Bad |
|------|-----|
| "bookstore" | "store" |
| "with CRUD, authentication" | "with auth stuff" |
| "using TypeScript, PostgreSQL" | "using a database" |

## Cancel anytime

```bash
/cancel
```

Progress is preserved for resume.

## Resume after cancel

```bash
/loop:run
```

(again with same or no brief)

## Integration with /loop:goal

For large multi-goal projects:

```bash
/loop:goal create-goals --brief "ship the API" \
  --goal "Schema::Design and implement" \
  --goal "API::Build CRUD endpoints" \
  --goal "Auth::Add authentication"

# Then run each goal through autopilot:
/loop:goal complete-goals  # Gets next story
/loop:run "build the schema story"  # Execute it
/loop:goal checkpoint ...  # Mark complete
# Repeat...
```

## State files written

| File | Location | Purpose |
|------|----------|---------|
| `spec.md` | `.omc/autopilot/` | Expanded specification |
| `autopilot-impl.md` | `.omc/plans/` | Implementation plan |
| `autopilot-state.json` | `.omc/state/` | Execution state |
| `ralph-state.json` | `.omc/state/` | Ralph executor state |
| `ultraqa-state.json` | `.omc/state/` | QA cycle state |

## Advanced config

In `.claude/omc.jsonc` or `~/.config/claude-omc/config.jsonc`:

```jsonc
{
  "autopilot": {
    "maxIterations": 10,
    "maxQaCycles": 5,
    "maxValidationRounds": 3,
    "pauseAfterExpansion": false,
    "pauseAfterPlanning": false,
    "skipQa": false,
    "skipValidation": false,
    "execution": "solo"  // or "team" for tmux-based
  }
}
```