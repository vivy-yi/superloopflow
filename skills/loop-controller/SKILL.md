---
name: loop-controller
description: State machine that drives /loop:next — decides the next phase based on state schema (v3.0)
---

# Loop Controller (v3.0)

State machine that drives `/loop:next`. Decides the next phase from the state schema — it's a **state machine, not a checklist**.

## State Schema (Single Source of Truth)

```yaml
state:
  # Project identity
  project_name: str                    # e.g., "x-pipeline"
  user_intent: str                     # new feature | fix | refactor | docs | bootstrap
  project_root: str                    # absolute path

  # Phase 0
  git_initialized: bool                # .git/ exists
  readme_exists: bool                  # README.md exists
  language_detected: str | null        # python | typescript | go | rust | etc.

  # Phase 0.5
  tools_inventory_exists: bool         # .tools-inventory.yaml exists

  # Phase 1
  brainstorming_done: bool             # Spec file written & user-approved
  spec_path: str | null                # Path to spec file

  # Phase 3
  plan_path: str | null                # Path to implementation plan

  # Phase 4
  agent_guide_exists: bool             # AGENT_GUIDE.md exists
  rules_count: int                     # common + language-specific rules
  skills_count: int                    # meta + stage skills
  pipeline_manifest_exists: bool       # pipeline_defs/<pipeline>.yaml
  agents_registry_exists: bool         # agents/registry.yaml

  # Phase 5
  code_committed: bool                 # at least one commit beyond init

  # Phase 6
  tests_passing: bool                  # all tests green
  review_passed: bool                  # code review completed

  # Phase 7
  continuous_learning_done: bool       # instincts/skills extracted
```

## Core Algorithm

```python
def next_action(state):
    # Phase 0: Bootstrap — must come first, ALWAYS
    if not state.git_initialized:
        return ("PHASE_0", "manual: git init + README + .gitignore",
                "Create project root, init git, write README")

    # Phase 0.5: Tools inventory — after git init, before any code work
    if not state.tools_inventory_exists:
        return ("PHASE_0_5", "omc-doctor + harness-audit",
                "Detect installed tools, create .tools-inventory.yaml")

    # Capture user_intent on first real interaction (Phase 1 trigger)
    if not state.user_intent:
        return ("PHASE_1_PRE", "ask user: 'what are you trying to do?'",
                "Capture user_intent (new feature | fix | refactor | docs)")

    # Phase 1: Requirements (skip if intent is 'fix' or 'refactor')
    if not state.brainstorming_done and state.user_intent == "new feature":
        return ("PHASE_1", "brainstorming skill or /omc:deep-interview",
                "Clarify requirements with user, one question at a time")

    # Phase 2: Spec authoring (manual from brainstorming output)
    if not state.spec_path and state.user_intent == "new feature":
        return ("PHASE_2", "manual",
                "Enrich spec with scope/acceptance/risks")

    # Phase 3: Planning
    if not state.plan_path and state.user_intent == "new feature":
        return ("PHASE_3", "writing-plans skill",
                "Decompose spec into bite-sized TDD tasks")

    # Phase 4: Meta-documentation (5 sub-phases)
    if not state.agent_guide_exists:
        return ("PHASE_4A", "manual",
                "Write AGENT_GUIDE.md (the main contract)")
    if state.rules_count < 3:
        return ("PHASE_4B", "manual",
                "Create common + language-specific rules")
    if state.skills_count < 3:
        return ("PHASE_4C", "skill-creator skill",
                "Create meta + stage skills")
    if not state.pipeline_manifest_exists:
        return ("PHASE_4D", "manual",
                "Write pipeline_defs/<pipeline>.yaml")
    if not state.agents_registry_exists:
        return ("PHASE_4F", "/omc:deep-interview + manual",
                "Create agents/registry.yaml + agents/<name>.md")

    # Phase 5: Implementation
    if not state.code_committed:
        return ("PHASE_5", "/omc:autopilot or /omc-teams N:executor",
                "Implement plan with TDD")

    # Phase 6: Verification
    if not state.tests_passing:
        return ("PHASE_6A", "test-driven-development skill + /omc:ralph",
                "Fix failing tests with persistent loop")
    if not state.review_passed:
        return ("PHASE_6B", "/ecc:code-review (or language-specific)",
                "Run code review")

    # Phase 7: Continuous improvement
    if not state.continuous_learning_done:
        return ("PHASE_7", "continuous-learning-v2 + writing-skills",
                "Extract instincts, evolve into skills")

    return ("DONE", None, "Project is production-ready")
```

## Human-Readable Decision Table

| If state shows... | Then next action is... |
|--------------------|------------------------|
| No `.git/` directory | **Phase 0:** `git init` + README + `.gitignore` |
| `.git/` exists but no `.tools-inventory.yaml` | **Phase 0.5:** `/loop:detect-tools` |
| No `user_intent` captured | **Phase 1 PRE:** Ask user what they're trying to do |
| `user_intent == "new feature"` AND no spec | **Phase 1:** `brainstorming` skill or `/omc:deep-interview` |
| Spec exists but no scope/acceptance/risks | **Phase 2:** Manually enrich the spec |
| No plan file | **Phase 3:** `writing-plans` skill |
| No `AGENT_GUIDE.md` | **Phase 4A:** Write the agent contract (manual) |
| `< 3` rules | **Phase 4B:** Create common + language-specific rules (manual) |
| `< 3` skills | **Phase 4C:** Create meta + stage skills (`skill-creator` skill) |
| No pipeline manifest | **Phase 4D:** Write `pipeline_defs/<name>.yaml` (manual) |
| No agents registry | **Phase 4F:** Use `/omc:deep-interview` + manual |
| No code committed | **Phase 5:** `/omc:autopilot "<plan>"` or `/omc-teams N:executor` |
| Tests failing | **Phase 6A:** `test-driven-development` + `/omc:ralph` |
| No review | **Phase 6B:** `/ecc:code-review` (or language-specific) |
| No instinct extraction | **Phase 7:** `continuous-learning-v2` + `writing-skills` |
| All above ✅ | **DONE** — production-ready |

## Loop Reset Semantics (Intent-Aware Routing)

The loop is **intent-aware** — different intents route to different phases.

| User Intent | Skip To | Phase Sequence |
|-------------|---------|----------------|
| Bootstrap (new project) | Phase 0 | 0 → 0.5 → 1 → 2 → 3 → 4 → 5 → 6 → 7 |
| Add a new feature | Phase 1 | 1 → 2 → 3 → 5 → 6 → 7 (skip 0, skip 4 if done) |
| Fix a bug | Phase 5 | 5 → 6 (write failing test first via TDD) |
| Refactor existing code | Phase 5 | 5 → 6 (existing tests must still pass) |
| Add a new pipeline | Phase 4D + 5 | 4D → 5 → 6 → 7 |
| Add a new tool | Phase 4C + 5 | 4C → 5 → 6 |
| Update documentation | Phase 4 | 4A → 4B → 4C (no code) |
| Extract a skill from session | Phase 7 | 7 only |

**Rule:** If `state.code_committed = True` and Phase 4 outputs exist, treat the project as "established" — only re-run the affected phases, not the full loop.

## When to Suggest Plugin Installation

If a phase has 0 providers, the capability lookup returns empty. Suggest:

| Phase | Missing Capability | Suggest Installation |
|-------|-------------------|---------------------|
| 1 (brainstorming) | requirements-clarification | superpowers or omc |
| 3 (planning) | plan-decomposition | superpowers |
| 5 (implementation) | implementation-autopilot | omc |
| 6 (review) | code-review | ecc |
| 7 (learning) | continuous-learning | ecc |

## Key Fields

- `user_intent` — captured on first interaction, used to skip phases (e.g., "bug fix" skips 1-3)
- `language_detected` — set in Phase 0, used to pick language-specific skills/commands in Phase 6

## Phase 0 Bug Fix (v1 → v2)

> **Note:** The v1 bug routed to `brainstorming` even when git was missing — that wasted cycles. v2/v3 routes to Phase 0 first when git is absent.

```python
# v1 BUG: skipped Phase 0 when git was missing
if not state.brainstorming_done:
    return Phase 1  # WRONG if git not initialized

# v2/v3 FIX: always check Phase 0 first
if not state.git_initialized:
    return Phase 0  # Always first
```

## State File Location

State is persisted in `.loop-state/state.yaml` in the project root. The loop controller reads this file at session start to restore context.

## Usage

```bash
/loop:status    # Show current state
/loop:next      # Decide and suggest next action
```