---
name: loop-state
description: State schema and transitions for the LOOP state machine (v3.0)
---

# Loop State (v3.0)

The state schema is the **single source of truth** for the LOOP. All phase decisions derive from this schema.

## State Schema

```yaml
state:
  # === Project Identity ===
  project_name: str                    # <project-name>, e.g., "x-pipeline"
  user_intent: str                     # new feature | fix | refactor | docs | bootstrap | question
  project_root: str                    # absolute path to project

  # === Phase 0: Bootstrap ===
  git_initialized: bool                # .git/ directory exists
  readme_exists: bool                  # README.md exists
  language_detected: str | null        # python | typescript | go | rust | java | cpp | csharp | kotlin | flutter | unknown

  # === Phase 0.5: Tools Inventory ===
  tools_inventory_exists: bool         # .tools-inventory.yaml exists

  # === Phase 1: Requirements ===
  brainstorming_done: bool             # Spec file written & user-approved
  spec_path: str | null                # Path to spec file (from brainstorming)
  spec_enriched: bool                  # Spec has scope/acceptance/risks filled

  # === Phase 3: Planning ===
  plan_path: str | null                # Path to implementation plan
  plan_approved: bool                  # Plan reviewed and approved

  # === Phase 4: Meta-Documentation ===
  agent_guide_exists: bool             # AGENT_GUIDE.md (or CLAUDE.md) exists
  agent_guide_lines: int               # Line count for quality gate
  rules_count: int                     # Count of rules in .claude/rules/
  rules_common_count: int              # Count of rules in .claude/rules/common/
  rules_lang_count: int                # Count of language-specific rules
  skills_count: int                    # Count of skills in .claude/skills/
  skills_meta_count: int               # Count of meta skills
  skills_stage_count: int              # Count of stage skills
  pipeline_manifest_exists: bool       # pipeline_defs/<name>.yaml exists
  pipeline_manifest_path: str | null   # Path to pipeline manifest
  agents_registry_exists: bool         # agents/registry.yaml exists
  agents_count: int                    # Number of agent roles defined

  # === Phase 5: Implementation ===
  code_committed: bool                 # At least one commit beyond init
  commits_count: int                   # Number of commits since init
  tests_exist: bool                    # Test files exist
  build_exists: bool                   # Build step defined (Makefile, etc.)

  # === Phase 6: Verification ===
  tests_passing: bool                  # All tests green (make test passes)
  tests_coverage: float | null         # Test coverage percentage
  lint_passing: bool                   # Linter passes
  review_passed: bool                  # Code review completed without critical findings
  review_rounds: int                   # Number of review rounds
  build_passing: bool                  # Build succeeds (if applicable)

  # === Phase 7: Continuous Learning ===
  continuous_learning_done: bool       # Instincts/skills extracted this session
  skills_extracted: int                # Number of new skills extracted
  instincts_captured: int              # Number of instincts recorded
```

## State Transitions

### Phase 0: Bootstrap
```
EMPTY → git_initialized=T, readme_exists=T, language_detected=<lang>
```

### Phase 0.5: Tools Inventory
```
git_initialized=T → tools_inventory_exists=T
```

### Phase 1: Requirements
```
user_intent set → brainstorming_done=T, spec_path=<path>
```

### Phase 2: Spec Enrichment
```
spec_path set → spec_enriched=T
```

### Phase 3: Planning
```
spec_enriched=T → plan_path=<path>, plan_approved=T
```

### Phase 4: Meta-Docs
```
plan_approved=T → agent_guide_exists=T, rules_count>=3, skills_count>=3,
                  pipeline_manifest_exists=T, agents_registry_exists=T
```

### Phase 5: Implementation
```
pipeline_manifest_exists=T → code_committed=T, tests_exist=T, build_exists=T
```

### Phase 6: Verification
```
code_committed=T → tests_passing=T, lint_passing=T, review_passed=T, build_passing=T
```

### Phase 7: Continuous Learning
```
tests_passing=T, review_passed=T → continuous_learning_done=T
```

## Intent Routing

The `user_intent` field determines which phases are skipped:

| Intent | Skipped Phases | Notes |
|--------|---------------|-------|
| `new feature` | none | Full loop |
| `fix` | 1, 2, 3, 4 | Direct to Phase 5 with TDD |
| `refactor` | 1, 2, 3, 4 | Direct to Phase 5, existing tests must pass |
| `docs` | 1, 2, 3, 5, 6 | Only Phase 4 meta-docs |
| `bootstrap` | all | Start from Phase 0 |
| `question` | all | Answer directly, no phase needed |

## State File Location

State is persisted in `.loop-state/state.yaml`:

```yaml
# .loop-state/state.yaml
project_name: my-project
user_intent: new feature
project_root: /path/to/my-project
git_initialized: true
readme_exists: true
language_detected: python
tools_inventory_exists: true
brainstorming_done: true
spec_path: docs/superpowers/specs/2026-06-29-myfeature-design.md
spec_enriched: true
plan_path: docs/superpowers/plans/2026-06-29-myfeature-plan.md
plan_approved: true
agent_guide_exists: true
rules_count: 5
skills_count: 4
pipeline_manifest_exists: true
agents_registry_exists: true
code_committed: true
tests_passing: true
review_passed: true
continuous_learning_done: false
```

## How to Update State

The loop controller updates state after each phase completes:

```bash
# After Phase 0:
yq -i '.git_initialized = true | .readme_exists = true | .language_detected = "python"' .loop-state/state.yaml

# After Phase 1:
yq -i '.brainstorming_done = true | .spec_path = "docs/superpowers/specs/..."' .loop-state/state.yaml
```

## State Validation

Before entering Phase 5, validate that Phase 4 outputs exist:

```bash
# Validate Phase 4 completion
test -f AGENT_GUIDE.md && \
test $(find .claude/rules/ -name "*.md" | wc -l) -ge 3 && \
test $(find .claude/skills/ -name "SKILL.md" | wc -l) -ge 3 && \
test -f pipeline_defs/*.yaml && \
test -f agents/registry.yaml
```

## Thresholds

These thresholds are tunable per project:

| Field | Threshold | Rationale |
|-------|-----------|-----------|
| `rules_count` | >= 3 | Enough to establish patterns |
| `skills_count` | >= 3 | Meta + at least one stage skill |
| `review_rounds` | <= 2 | Escalate after 2 rounds |
| `tests_coverage` | >= 80% | Standard coverage target |

## Failure Mode: State Drift

If the loop controller's state doesn't match reality, it will route to the wrong phase.

**Symptom:** "I'm in Phase 5 but the loop keeps suggesting Phase 4"

**Check:**
```bash
# Verify actual state
ls -la .loop-state/state.yaml
cat .loop-state/state.yaml

# Compare with reality
test -f AGENT_GUIDE.md && echo "AGENT_GUIDE exists" || echo "AGENT_GUIDE missing"
find .claude/rules/ -name "*.md" | wc -l
```

**Fix:** Update `.loop-state/state.yaml` to match reality, or create it if missing.