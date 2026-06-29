# Superloopflow — Loop Engineering Orchestrator

> **Version:** 3.0 (2026-06-29)
> **Audience:** Human developer + AI agent
> **Purpose:** Define a **repeatable, state-machine-driven loop** for taking a project from empty directory to production-ready, integrating Superpowers + OMC + ECC plugins.

> "I don't prompt Claude anymore. I design loops that prompt Claude and decide what to do."
> — Boris Cherny, Anthropic Claude Code

---

## 1. TL;DR

1. **v3.0 is ecosystem-aware:** 22+ enabled plugins, 214+ inferred capabilities
2. **Eight phases** — 0 bootstrap → 0.5 tools → 1 brainstorm → 2 spec → 3 plan → 4 meta-docs → 5 implement → 6 verify → 7 improve
3. **The loop controller** decides the next phase from a state schema — it's a state machine, not a checklist
4. **Phase 4 (meta-docs) is the most-skipped phase** — it's also the most important for AI consistency
5. **All commands verified** against the actual plugin inventories (no hallucinated commands)
6. **This is a TEMPLATE** — adapt it to your project
7. **Failure modes are explicit** — when things go wrong, don't paper over them

---

## 2. The Three Plugin Roles

| Plugin | Role | What It Provides |
|--------|------|------------------|
| **Superpowers** | Process discipline (the backbone) | Skills: `brainstorming`, `writing-plans`, `subagent-driven-development`, `test-driven-development`, `verification-before-completion`, `writing-skills`, `requesting-code-review`, `systematic-debugging` |
| **OMC** (oh-my-claudecode) | Multi-agent orchestration & persistent execution | Commands: `/omc:autopilot`, `/omc:ralph`, `/omc:ralplan`, `/omc:deep-interview`, `/omc-teams` |
| **ECC** (everything-claude-code) | Quality, learning & language-specific patterns | Commands: `/ecc:code-review`, `/ecc:build-fix`, `/ecc:harness-audit`, `/ecc:learn`, `/ecc:evolve`, language-specific reviewers |

**Rule of thumb:**
- **Brainstorming / Spec / Plan / TDD** → Superpowers skills
- **Multi-agent execution / persistent loops / team coordination** → OMC
- **Code review / build-fix / language patterns / continuous learning** → ECC

---

## 3. High-Level Flow

```
┌──────────────────────────────────────────────────────────────────┐
│                  Project Development Loop                          │
└──────────────────────────────────────────────────────────────────┘
                                │
        ┌───────────┬───────────┼───────────┬───────────┬───────────┐
        ▼           ▼           ▼           ▼           ▼           ▼
   [Phase 0]   [Phase 0.5] [Phase 1]   [Phase 2]   [Phase 3]   [Phase 4]
   Bootstrap   Tools      Brainstorm  Spec        Plan        Meta-docs
   (git init)  Inventory  (skill)     (manual)    (skill)     (manual)
        │           │           │           │           │           │
        └───────────┴─────┬─────┴───────────┴───────────┴───────────┘
                          │
                          ▼
                    [Phase 5]
                    Implement
                    (OMC team / autopilot)
                          │
                          ▼
                    [Phase 6]
                    Verify (tests + review)
                          │
                          ▼
                    [Phase 7]
                    Continuous learning
                          │
                          ▼
                  [Production-Ready]
                          │
              (Loop back to any phase on user request)
```

---

## 4. State Schema

The state schema is the **single source of truth** for the loop.

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

---

## 5. Loop Controller Algorithm

```python
def next_action(state):
    # Phase 0: Bootstrap — must come first, ALWAYS
    if not state.git_initialized:
        return ("PHASE_0", "manual: git init + README + .gitignore")

    # Phase 0.5: Tools inventory — after git init, before any code work
    if not state.tools_inventory_exists:
        return ("PHASE_0_5", "omc-doctor + harness-audit")

    # Capture user_intent on first real interaction
    if not state.user_intent:
        return ("PHASE_1_PRE", "ask user: what are you trying to do?")

    # Phase 1: Requirements (skip if intent is 'fix' or 'refactor')
    if not state.brainstorming_done and state.user_intent == "new feature":
        return ("PHASE_1", "brainstorming skill or /omc:deep-interview")

    # Phase 2: Spec authoring (manual from brainstorming output)
    if not state.spec_path and state.user_intent == "new feature":
        return ("PHASE_2", "manual: enrich spec with scope/acceptance/risks")

    # Phase 3: Planning
    if not state.plan_path and state.user_intent == "new feature":
        return ("PHASE_3", "writing-plans skill")

    # Phase 4: Meta-documentation (5 sub-phases)
    if not state.agent_guide_exists:
        return ("PHASE_4A", "manual: write AGENT_GUIDE.md")
    if state.rules_count < 3:
        return ("PHASE_4B", "manual: create rules")
    if state.skills_count < 3:
        return ("PHASE_4C", "skill-creator skill")
    if not state.pipeline_manifest_exists:
        return ("PHASE_4D", "manual: write pipeline_defs YAML")
    if not state.agents_registry_exists:
        return ("PHASE_4F", "deep-interview + manual: create agents/")

    # Phase 5: Implementation
    if not state.code_committed:
        return ("PHASE_5", "/omc:autopilot or /omc-teams N:executor")

    # Phase 6: Verification
    if not state.tests_passing:
        return ("PHASE_6A", "test-driven-development + /omc:ralph")
    if not state.review_passed:
        return ("PHASE_6B", "/ecc:code-review (or language-specific)")

    # Phase 7: Continuous improvement
    if not state.continuous_learning_done:
        return ("PHASE_7", "continuous-learning-v2 + writing-skills")

    return ("DONE", None, "Project is production-ready")
```

---

## 6. Commands (25)

### Bootstrap
- `/loop:init` — git init, README, .gitignore, .env.example, Makefile
- `/loop:setup` — read-only plugin discovery + build registry
- `/loop:discover` — list all inferred capabilities by phase
- `/loop:capability <name>` — look up specific capability
- `/loop:detect-tools` — generate `.tools-inventory.yaml` + run omc-doctor + harness-audit

### Loop Controller
- `/loop:status` — show current state
- `/loop:next` — decide next phase based on state machine

### Phase 1-3: Discovery (bridged)
- `/loop:brainstorm` → Superpowers `brainstorming` or OMC `/omc:deep-interview`
- `/loop:spec` → manual enrichment of brainstorming output
- `/loop:plan` → Superpowers `writing-plans` skill

### Phase 4: Meta-Docs (own implementation)
- `/loop:create-entry-files` — create AGENT_GUIDE.md skeleton
- `/loop:create-agent-guide` — write AGENT_GUIDE.md
- `/loop:create-rules` — create .claude/rules/ structure
- `/loop:create-skills` — create .claude/skills/ structure
- `/loop:create-pipeline-manifest` — create pipeline_defs/<name>.yaml
- `/loop:create-agent-registry` — create agents/registry.yaml

### Phase 5-7: Execution & Verification (bridged)
- `/loop:implement` → OMC `/omc:autopilot` or `/omc-teams`
- `/loop:ralph` → OMC `/omc:ralph` (persistent fix loop)
- `/loop:review` → ECC `/ecc:code-review` (or language-specific)
- `/loop:verify` → Superpowers `verification-before-completion`
- `/loop:evolve` → ECC `/ecc:evolve` + Superpowers `writing-skills`

### NEW: Loop Engineering Core (v0.5)
- `/loop:goal` → OMC `ultragoal` — durable multi-goal workflow with ledger
- `/loop:run` → OMC `autopilot` — full autonomous execution
- `/loop:harness` → ECC `autonomous-agent-harness` — persistent scheduling + memory

---

## 7. Skills (13)

### Core Skills
- `loop-state` — State schema and transitions
- `loop-controller` — State machine decision logic
- `plugin-bridge` — v0.4 read-only discovery pattern
- `capability-catalog` — Three-layer capability discovery
- `ecosystem` — Plugin categories and lifecycle
- `quadripartite` — Agent + Skill + Code + Agentic Tools governance

### Meta-Doc Skills
- `meta-doc-author` — How to author project meta-documentation
- `agent-registry-author` — How to author agent roles
- `tool-inventory-audit` — How to maintain tools

### Loop Engineering Skills (v0.5)
- `loop-goal` — Durable multi-goal workflow (OMC ultragoal)
- `loop-run` — Full autonomous execution (OMC autopilot)
- `loop-harness` — Persistent autonomous operation (ECC harness)

---

## 8. Phase 0: Bootstrap (~30 min)

**Trigger:** `state.git_initialized == False`

```bash
mkdir -p ~/projects/<project-name> && cd ~/projects/<project-name>
git init
git config user.name "<your-name>"
git config user.email "<you@local>"

# Create README.md
cat > README.md <<'EOF'
# <project-name>

<one-paragraph description>

## Quick Start
<how to set up and run>
EOF

# Detect language and create .gitignore
# python: __pycache__/, *.py[cod], .pytest_cache/, .venv/
# typescript: node_modules/, dist/, .next/, .env
# go: bin/, *.exe, vendor/
# rust: target/, Cargo.lock
# java: target/, *.class, .gradle/, build/

git add . && git commit -m "chore: initialize project"
```

**Exit:** `.git/` + `README.md` + `.gitignore` + `language_detected != null`

---

## 9. Phase 0.5: Tools Inventory (~15 min)

**Trigger:** `git_initialized == True AND tools_inventory_exists == False`

```bash
# Detect installed agentic tools
/omc:omc-doctor
/ecc:harness-audit

# Create .tools-inventory.yaml
```

**Purpose:** Explicit declaration of agentic tools so agents can reason about "which tool is best for this task."

---

## 10. Phase 1: Requirements Clarification (~1-2 hours)

**Trigger:** `brainstorming_done == False AND user_intent == "new feature"`

```text
use the brainstorming skill
Initial idea: <one sentence about what the user wants>
```

OR:

```bash
/omc:deep-interview <your initial idea>
```

**Exit:** Spec at `docs/superpowers/specs/YYYY-MM-DD-<topic>-design.md`

---

## 11. Phase 2: Spec Authoring (~30 min)

**Trigger:** `spec_path != null` but lacks structure

Manual enrichment:
- Add **Scope** section (in / out)
- Add **Acceptance Criteria** (measurable, testable)
- Add **Risks & Mitigations** table
- Add **Dependencies**
- Add **Non-functional requirements**

**Exit:** Spec with all sections filled, self-reviewed, user-approved

---

## 12. Phase 3: Implementation Planning (~1-2 hours)

**Trigger:** `plan_path == null AND spec_path != null`

```text
use the writing-plans skill
Spec path: <spec_path>
```

**Exit:** Plan at `docs/superpowers/plans/YYYY-MM-DD-<feature>-plan.md`

---

## 13. Phase 4: Meta-Documentation (~1 day)

**Trigger:** Plan exists, but meta-docs missing

### 4A: AGENT_GUIDE.md
Write `AGENT_GUIDE.md` at project root (500-800 lines):
- Rule Zero (project philosophy)
- First Interaction (onboarding)
- What This Project Is
- Decision Communication Contract
- Mandatory Preflight
- Capability Discovery
- Tool Families
- Pipeline Manifests
- Stage Director Skills
- Reviewer Protocol
- Human Checkpoint Protocol
- Communication Protocol
- Style Guides
- Layer Map
- Quick Lookup
- What Not To Do

### 4B: Rules (`.claude/rules/`)
- `common/` — coding-style, git-workflow, testing, performance, security, patterns, agents
- `<lang>/` — language-specific rules

### 4C: Skills (`.claude/skills/`)
- Meta skills: reviewer, checkpoint-protocol, onboarding, skill-creator
- Stage skills: per pipeline stage
- `INDEX.md` — skills catalog

### 4D: Pipeline Manifest
Create `pipeline_defs/<pipeline-name>.yaml`

### 4F: Agent Registry
Create `agents/registry.yaml` + `agents/<name>.md` per role

**Exit:** `agent_guide_exists=True, rules_count>=3, skills_count>=3, pipeline_manifest_exists=True, agents_registry_exists=True`

---

## 14. Phase 5: Implementation (~1-5 days)

**Trigger:** `code_committed == False`

### Option A: Single-agent autopilot
```bash
/omc:autopilot "implement the plan at <plan_path>"
```

### Option B: Multi-agent team
```bash
/omc-teams  # Then: 1 planner, N executors, 1 reviewer
```

### Option C: Subagent-driven development
```text
use the subagent-driven-development skill
Plan: <plan_path>
```

### Per-task TDD loop
1. Write failing test first
2. Run test → expect FAIL
3. Write minimal implementation
4. Run test → expect PASS
5. Self-review
6. Commit

### Persistent fixing
```bash
/omc:ralph "fix failing tests until green"
```

**Exit:** `code_committed = True`

---

## 15. Phase 6: Verification (~1-2 hours)

**Trigger:** `code_committed == True`

### Step 1: Pre-completion verification
```text
use the verification-before-completion skill
Claim: "I have finished implementing <plan_path>"
```

### Step 2: Run full test suite
```bash
make test
```

### Step 3: Code review
```bash
/ecc:code-review  # or language-specific: /ecc:python-review, /ecc:go-review
```

### Step 4: Handle findings
- **Critical** → Fix before declaring done
- **High** → Fix or document why deferred
- **Medium/Low** → Document, schedule for later

**Exit:** `tests_passing = True AND review_passed = True`

---

## 16. Phase 7: Continuous Improvement (~30 min)

**Trigger:** `tests_passing == True AND review_passed == True`

```bash
/ecc:instinct-status
/ecc:evolve
use the writing-skills skill
```

### Extract reusable patterns
For each recurring instinct, cluster into a new skill and update `skills/INDEX.md`.

```bash
git add .claude/skills/ skills/
git commit -m "feat(skills): extract <pattern-name> from session <date>"
```

**Exit:** `continuous_learning_done = True`

---

## 17. Quick Reference: Command Cheatsheet

| Phase | Command / Skill | Plugin |
|-------|-----------------|--------|
| 0 | `git init` + manual | manual |
| 0.5 | `/loop:detect-tools` + omc-doctor + harness-audit | OMC + ECC |
| 1 | `brainstorming` skill or `/omc:deep-interview` | Superpowers / OMC |
| 2 | Manual (enrich spec) | manual |
| 3 | `writing-plans` skill | Superpowers |
| 4A | Manual (AGENT_GUIDE.md) | manual |
| 4B | Manual (rules) | manual |
| 4C | `skill-creator` skill | Superpowers |
| 4D | Manual (pipeline_defs YAML) | manual |
| 4F | `/omc:deep-interview` + manual | OMC + manual |
| 5 | `/omc:autopilot` or `/omc-teams` | OMC |
| 5 (fix) | `/omc:ralph` | OMC |
| 6A | `verification-before-completion` + `test-driven-development` | Superpowers |
| 6A (fix) | `systematic-debugging` skill | Superpowers |
| 6B | `/ecc:code-review` (or `/ecc:<lang>-review`) | ECC |
| 6 (build) | `/ecc:build-fix` | ECC |
| 7 | `/ecc:instinct-status` + `/ecc:evolve` + `writing-skills` | ECC + Superpowers |

---

## 18. Loop Reset Semantics

The loop is **intent-aware** — different intents route to different phases.

| User Intent | Skip To | Phase Sequence |
|-------------|---------|----------------|
| Bootstrap (new project) | Phase 0 | 0 → 0.5 → 1 → 2 → 3 → 4 → 5 → 6 → 7 |
| Add a new feature | Phase 1 | 1 → 2 → 3 → 5 → 6 → 7 (skip 0, skip 4 if done) |
| Fix a bug | Phase 5 | 5 → 6 (TDD loop) |
| Refactor existing code | Phase 5 | 5 → 6 (existing tests must pass) |
| Add a new pipeline | Phase 4D + 5 | 4D → 5 → 6 → 7 |
| Add a new tool | Phase 4C + 5 | 4C → 5 → 6 |
| Extract a skill | Phase 7 | 7 only |

---

## 19. Anti-Patterns

1. **Skip Phase 4** — "We'll write docs as we go."
2. **Use placeholder meta-docs** — "I'll fill in AGENT_GUIDE later."
3. **Skip self-review** — "Tests pass, ship it."
4. **Run Phase 5 without Phase 4** — "We don't need docs, just code."
5. **Use one agent for everything** — "I don't need OMC team mode."
6. **Bypass the loop controller** — "I'll just write code directly."
7. **Claim "done" before verification** — "I implemented the feature."
8. **Ignore language detection** — Use `/ecc:code-review` instead of `/ecc:python-review`
9. **Treat Phase 7 as optional** — "We don't have time for skill extraction."
10. **Forget to update state schema** — Loop controller thinks phase isn't done

---

## 20. Failure Modes & Recovery

| Failure | Symptom | Recovery |
|---------|---------|----------|
| Vague spec | Phase 2 reveals unmeasurable AC | Loop back to Phase 1 |
| Tasks >30 min | Implementation 5x longer | Re-run writing-plans with decomposition feedback |
| AGENT_GUIDE >2000 lines | No one reads it | Split into focused sub-docs |
| Persistent test failures | `/omc:ralph` hits max iterations | Use `systematic-debugging` manually |
| Code review criticals | New criticals in round 2+ | Escalate to user after 2 rounds |
| Meta-docs not followed | Inconsistent outputs | Fix the docs first, then re-run |

---

## 21. Honest Limitations

This document is a **template**, not a complete recipe.

### What this IS:
- A state-machine framing with explicit phase transitions
- A verified command inventory
- An intent-aware routing system
- A failure-mode catalog

### What this IS NOT:
- A turnkey recipe
- A substitute for plugin docs
- Exhaustive
- Up-to-date forever

---

## 22. The Quadripartite Model

Modern AI-assisted development has **four first-class elements**:

| Element | Role | Where Defined | How Reviewed |
|---------|------|---------------|--------------|
| **Agent** | Who does the work | `agents/registry.yaml` + `agents/<name>.md` | Agent role review |
| **Skill** | What knowledge is applied | `skills/<domain>/<name>.md` + `skills/INDEX.md` | Skill review |
| **Code** | The implementation | `src/` | Code review |
| **Agentic Tools** | The platform/framework | `.tools-inventory.yaml` | Tool audit |

---

## 23. The Capability Discovery Protocol

### Tier 1: Must-have capabilities (~10)

| Capability | Default Provider |
|-----------|------------------|
| requirements-clarification | superpowers/brainstorming |
| plan-decomposition | superpowers/writing-plans |
| spec-authoring | planning-with-files/prd |
| implementation-autopilot | omc/autopilot |
| code-review | ecc/code-review |
| test-driven-development | superpowers/tdd |
| verification-before-completion | superpowers/verification |
| continuous-learning | ecc/continuous-learning-v2 |

### Tier 2: Domain-specific (200+)

Auto-inferred from plugin names and descriptions. Run `/loop:discover` to see.

### Tier 3: Project-specific

Declared in `pipeline_defs/<pipeline>.yaml`.

---

## 24. The Plugin Ecosystem

| Category | Examples | LOOP phases |
|----------|----------|-------------|
| **Process discipline** | superpowers, plugin-dev | 1, 3, 5, 6 |
| **Orchestration** | omc, agent-orchestration | 5, 6, 7 |
| **Quality + skills** | ecc, code-review, pr-review-toolkit | 6, 7 |
| **Backend** | backend-development, llm-application-dev | 5, 6 |
| **Frontend** | frontend-design, frontend-mobile-development | 5, 6 |
| **Meta** | plugin-dev, feature-dev | 0, 7 |

### Plugin Discovery Rule (v0.4)

- ✅ Read `~/.claude/settings.json`
- ✅ Read each plugin's `plugin.json` + `commands/` + `skills/`
- ❌ **NEVER write** to `~/.claude/plugins/`

---

## 25. Setup

```bash
# Add to ~/.claude/settings.json:
{
  "extraKnownMarketplaces": {
    "loop-workflow": { "source": { "path": "/Volumes/waku/大模型/AI-生成/AI短剧/loop-workflow" } }
  },
  "enabledPlugins": { "superloopflow@loop-workflow": true }
}

# In Claude Code:
/loop:setup
/loop:discover
/loop:next
```

## License

MIT