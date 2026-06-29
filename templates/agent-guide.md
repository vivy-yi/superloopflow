# <Project Name> Agent Guide

> **Last updated:** <DATE>
> **Audience:** AI coding assistants
> **Read this BEFORE responding to any user message.**

This guide is the **operating contract** between the AI agent and the <Project Name> project. All conventions, routing rules, and quality standards live here.

---

## Rule Zero — <PROJECT_RULE_ZERO>

[Project-specific rule, e.g., "All code goes through TDD" or "All UI changes need design review"]

---

## First Interaction — Onboarding

[Skip logic — if user is vague, do X; if specific, go to Rule Zero]

---

## What This Project Is

<One-sentence identity>

**Tech Stack**: <list>

**Architecture**: <one paragraph>

See `PROJECT_CONTEXT.md` for full architecture, `docs/ARCHITECTURE.md` for deep dive.

---

## Decision Communication Contract

### Announce Before Execution
Before any paid/consequential call, state: tool, provider, model, reason, sample-vs-batch.

### Ask Before Major Changes
Get user approval before: switching provider, changing runtime, changing treatment, dropping approved elements.

### Escalate Blockers Explicitly
5-part: attempted / failed / category / options / recommendation.

### No Unilateral Substitutions
Provider swaps, model swaps, fallback tools — never without approval.

### Recommend, Don't Just List
Shortlist + tradeoffs + recommendation + wait for approval.

---

## Mandatory Preflight

```bash
make preflight
```

---

## Capability Discovery

```bash
python -c "from <project>.tools.registry import registry; registry.discover(); print(json.dumps(registry.capability_catalog(), indent=2))"
```

---

## Tool Families

<Project-specific: BaseTool / Provider / Selector pattern>

---

## Pipeline Manifests

[How to read pipeline_defs/*.yaml]

---

## Stage Director Skills

For each stage, there's a stage director skill at `skills/pipelines/<pipeline>/<stage>-director.md`. **Read before starting work on that stage.**

---

## Mandatory Skill Reading Order

1. `PROJECT_CONTEXT.md`
2. `docs/ARCHITECTURE.md` (if exists)
3. `.claude/skills/<project>-dev/SKILL.md`
4. `.claude/rules/common/*.md`
5. The relevant pipeline stage director skill
6. Any Layer 3 skill referenced by `agent_skills[]`

---

## Reviewer Protocol

After any stage, perform self-review. Severity: critical / suggestion / nitpick / investigation. Decision: PASS / REVISE / PASS_WITH_WARNINGS (max 2 rounds).

---

## Human Checkpoint Protocol

When `human_approval_default: true`, present and wait.

---

## Communication Protocol

- Plain language for user-facing
- Structured data for inter-stage
- Decision logs for non-trivial choices
- Conventional commits

---

## Style Guides

| Concern | Reference |
|---------|-----------|
| Coding style | `.claude/rules/common/coding-style.md` |
| Git commits | `.claude/rules/common/git-workflow.md` |
| Testing | `.claude/rules/common/testing.md` |
| Performance | `.claude/rules/common/performance.md` |
| Python specifics | `.claude/rules/python/style.md` |

---

## Layer Map (3-Layer Knowledge)

```
Layer 1: <project>/tools/ + pipeline_defs/  → executable
Layer 2: <project>/skills/                 → project conventions
Layer 3: .agents/skills/                    → vendor knowledge
```

---

## Quick Lookup

| Question | Look here |
|----------|-----------|
| What is this project? | `PROJECT_CONTEXT.md` |
| How is code organized? | `docs/ARCHITECTURE.md` |
| How do I run tests? | `Makefile` + `.claude/rules/common/testing.md` |
| How do I commit? | `.claude/rules/common/git-workflow.md` |
| What tools exist? | `python -c "...registry.capability_catalog()..."` |
| What pipelines exist? | `<project>/pipeline_defs/*.yaml` |
| How do I add a new tool? | `skills/meta/capability-extension.md` |
| How do I review my work? | `skills/meta/reviewer.md` |
| How do I checkpoint? | `skills/meta/checkpoint-protocol.md` |
| What major decisions were made? | `docs/DECISIONS.md` |
| What's the dev workflow? | `docs/DEV_WORKFLOW.md` |

---

## What Not To Do

- ❌ Don't write code without tests
- ❌ Don't skip preflight
- ❌ Don't substitute providers without approval
- ❌ Don't hide decisions in code
- ❌ Don't bypass pipeline manifests
- ❌ Don't auto-advance past human approval gates
- ❌ Don't review your own work with a perfect score
- ❌ Don't commit secrets
- ❌ Don't add Python orchestrators for creative decisions
- ❌ Don't use placeholder implementations
