# <Project Name> Skills Index

> L2 skills (project conventions) and Layer 3 (vendor knowledge) catalog.

## Knowledge Architecture

```
Layer 1: <project>/tools/ + pipeline_defs/   → "What tools + pipelines exist" (executable)
Layer 2: <project>/skills/                  → "How this project uses them" (conventions)
Layer 3: .agents/skills/                     → "How each technology works" (vendor docs)
```

## Layer 2 Skills

### core/
- TDD Workflow
- <Project-specific core skills>

### meta/
| Skill | File | Trigger |
|-------|------|---------|
| Reviewer | `meta/reviewer.md` | After completing any stage |
| Checkpoint Protocol | `meta/checkpoint-protocol.md` | Before human approval gates |
| Onboarding | `meta/onboarding.md` | First vague user message |
| Skill Creator | `meta/skill-creator.md` | Adding new skills |
| Capability Extension | `meta/capability-extension.md` | Adding new tools/pipelines |

### pipelines/<pipeline>/
| Skill | File | Stage | Tools |
|-------|------|-------|-------|
| <Stage> Director | `pipelines/<pipeline>/<stage>-director.md` | <stage> | <tools> |
