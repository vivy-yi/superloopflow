---
name: quadripartite
description: Four first-class elements: Agent + Skill + Code + Agentic Tools governance (v3.0)
---

# Quadripartite Model (v3.0)

Modern AI-assisted development has **four first-class elements**. All four deserve explicit governance.

## The Four Elements

| Element | Role | Example | Lifespan |
|---------|------|---------|----------|
| **Agent** | Who does the work (role + behavior) | `script-writer`, `reviewer`, `planner` | Long-lived; evolved by adding/removing roles |
| **Skill** | What knowledge is applied (patterns + best practices) | `brainstorming`, `tdd`, `ffmpeg-commands` | Long-lived; refined over time |
| **Code** | The implementation (executable) | `tools/`, `stages/`, `orchestrator.py` | Medium-lived; rewritten as skills evolve |
| **Agentic Tools** | The platform/framework (runtime) | OMC, ECC, Superpowers, Claude Code | Long-lived; upgrade with care |

## Why All Four Are First-Class

| Element | Without First-Class | With First-Class |
|---------|---------------------|------------------|
| Agent | Implicit roles; tribal knowledge; no way to onboard new agents | Explicit `agents/registry.yaml`; each role has contract + test + skill dependencies |
| Skill | Documentation in scattered READMEs; no catalog; no quality bar | `skills/INDEX.md`; standardized structure; peer review; evolution tracking |
| Code | "Just write code"; no architecture review | Architecture review; test coverage; dependency tracking |
| Agentic Tools | "Use whatever works"; no upgrade path; version conflicts | `.tools-inventory.yaml`; pinned versions; upgrade alerts; fallback strategies |

## Governance: One Per Element

| Element | Where Defined | How Reviewed | How Tested |
|---------|--------------|--------------|------------|
| Agent | `agents/registry.yaml` + `agents/<name>.md` | Agent role review (clear contract + skills + tools?) | Agent behavior tests (golden input → expected output) |
| Skill | `skills/<domain>/<name>.md` + `skills/INDEX.md` | Skill review (follows template + has Self-Evaluate?) | Skill example tests (does invoking produce right artifact?) |
| Code | `src/` | Code review (`/ecc:code-review`) | Unit + integration + E2E tests |
| Agentic Tools | `.tools-inventory.yaml` | Tool audit (`/ecc:harness-audit`) | Smoke test (does `omc-doctor` pass?) |

## Agent Registry

Create `agents/registry.yaml`:

```yaml
# agents/registry.yaml
agents:
  script-writer:
    purpose: "Generate dramatic scripts from user prompts"
    inputs: ["user_prompt", "style_id"]
    outputs: ["script.json"]
    skills_consumed:
      - skills/core/script-writing.md
    tools_invoked:
      - base:claude-llm
    success_criteria:
      - Schema-valid script.json
      - 2-3 characters
      - Style matches requested

  scene-planner:
    purpose: "Break script into visual shots"
    inputs: ["script.json"]
    outputs: ["scene_plan.json"]
    skills_consumed:
      - skills/core/scene-composition.md
    tools_invoked:
      - base:claude-llm
    success_criteria:
      - 8-10 shots per minute
      - Each shot 4-8 seconds

  asset-generator:
    purpose: "Produce audio, image, and video assets for each shot"
    inputs: ["scene_plan.json"]
    outputs: ["assets_manifest.json"]
    skills_consumed:
      - skills/providers/elevenlabs.md
      - skills/providers/flux.md
    tools_invoked:
      - elevenlabs_tts
      - flux_image
    success_criteria:
      - All shots have matching assets
      - Total cost ≤ budget

  composer:
    purpose: "Combine assets into final video"
    inputs: ["assets_manifest.json"]
    outputs: ["final_video.mp4", "render_report.json"]
    skills_consumed:
      - skills/core/ffmpeg-commands.md
    tools_invoked:
      - ffmpeg_compose
    success_criteria:
      - Playable MP4, 1080p, 30fps
      - Audio synced
```

## Agent File Template

Create `agents/<name>.md` for each role:

```markdown
# <role-name> Agent

## Role
[One sentence purpose]

## Input Contract
- input_1: type
- input_2: type

## Output Contract
- output_1: type
- output_2: type

## Skills to Read
- skills/<path>.md (mandatory)
- skills/<pipeline>/<skill>.md (mandatory)

## Tools to Use
- tool_name (via AgentRuntime)

## Process
1. Read skill X
2. Read skill Y
3. Compose system prompt
4. Call tool
5. Parse output, validate
6. Return result

## Self-Evaluation
- [ ] Output valid
- [ ] Meets success criteria
- [ ] Handles errors gracefully

## Failure Handling
- LLM returns invalid output: retry with explicit format instruction
- Tool unavailable: fallback to manual or error
```

## Skills INDEX

Create `skills/INDEX.md`:

```markdown
# Skills Index

## Meta Skills
| Skill | Purpose |
|-------|---------|
| `meta/reviewer.md` | Self-review protocol |
| `meta/checkpoint-protocol.md` | When/how to checkpoint |
| `meta/onboarding.md` | First-interaction protocol |
| `meta/skill-creator.md` | How to create new skills |

## Stage Skills
| Skill | Purpose | Pipeline |
|-------|---------|----------|
| `stages/script-writer.md` | Script generation | video-pipeline |
| `stages/scene-planner.md` | Scene decomposition | video-pipeline |
| `stages/asset-generator.md` | Asset production | video-pipeline |

## Provider Skills
| Skill | Purpose |
|-------|---------|
| `providers/elevenlabs.md` | ElevenLabs TTS API |
| `providers/flux.md` | Flux image generation |
| `providers/runway.md` | Runway video generation |
```

## Tools Inventory

Create `.tools-inventory.yaml`:

```yaml
agentic_tools:
  omc:
    version: "4.4.0+"
    required: true
    purpose: "Multi-agent orchestration"
    fallback: "manual team mode"
  ecc:
    version: "2.0.0+"
    required: true
    purpose: "Quality + skills"
    fallback: "manual review"
  superpowers:
    version: "6.0.3+"
    required: true
    purpose: "Process discipline"
    fallback: "skip Phase 1/3"

language_tools:
  python:
    version: "3.11+"
    required: true
  ffmpeg:
    version: "6+"
    required: true
```

## Lifecycle: The Quadripartite Pipeline

```
[Tool Discovery]    Phase 0.5
    ↓
[Bootstrap]         Phase 0
    ↓
[Brainstorming]     Phase 1
    ↓
[Spec]              Phase 2
    ↓
[Plan]              Phase 3
    ↓
[Skills]            Phase 4C
[Pipeline Manifest] Phase 4D
[Agent Registry]    Phase 4F
    ↓
[Implementation]    Phase 5
    ↓
[Verify]            Phase 6
    ↓
[Continuous Learn]  Phase 7
```

## Anti-Patterns

❌ **Don't have one of the four without a registry.** If you have skills but no `skills/INDEX.md`, you don't have Skills-First — you have docs.

❌ **Don't skip the Tools Inventory.** "We use OMC" is not enough. "We use OMC 4.4.0+ with fallback" is.

❌ **Don't have agents without contracts.** If an agent's input/output is implicit, it will break.

❌ **Don't treat tools as code.** Tools are external dependencies. Update them with `pip install --upgrade`, not by editing their source.

❌ **Don't evolve one element in isolation.** When you add a Skill, check if any Agent needs to update. When you add an Agent, check if any Pipeline needs to update.

## When to Migrate to Quadripartite

| Signal | Action |
|--------|--------|
| Managing 3+ agent types | Add Agent registry |
| Using 2+ agentic tool plugins | Add Tools Inventory |
| Explaining "which agent should do X" repeatedly | Formalize Agent registry |
| Tool upgrades break project | Add pinned versions |
| New team members take 2+ days to ramp | Add Agent registry + Tools Inventory |