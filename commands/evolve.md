---
name: evolve
description: Bridge to ECC /ecc:evolve and Superpowers writing-skills for continuous learning and skill extraction
---

# /superloopflow:evolve

Bridge to **ECC `/ecc:evolve`** and **Superpowers `writing-skills`** — extract patterns from session, evolve into reusable skills.

## What it does

1. Run `/ecc:instinct-status` to see learned patterns from this session
2. For each recurring instinct, cluster into a new skill
3. Use `writing-skills` skill for higher-quality skill output
4. Update `skills/INDEX.md` with new skills
5. Commit extracted skills

## When to use

- Phase 7 (continuous improvement)
- After a productive session with reusable patterns
- When you notice "I solved the same problem twice"

## What gets extracted

Patterns that recur across tasks:
- "Retry with exponential backoff for API calls"
- "Use pydantic models for config with env override"
- "Always validate input at system boundaries"
- "Use repository pattern for data access"

## What NOT to extract

- Trivial 10-line functions
- Project-specific hardcoded values
- One-off solutions that won't repeat

## Test: Is this skill-worthy?

"Would a new agent, reading this skill, produce better output than without it?"

If no → don't create the skill.

## Bridge targets

| Provider | Command/Skill | Purpose |
|----------|--------------|---------|
| ECC | `/ecc:instinct-status` | See learned patterns |
| ECC | `/ecc:evolve` | Cluster instincts into skills |
| Superpowers | `writing-skills` skill | Higher-quality skill authoring |

## Usage

```bash
# Step 1: See what instincts were captured
/ecc:instinct-status

# Step 2: Evolve instincts into skills
/ecc:evolve

# OR use writing-skills for higher quality
use the writing-skills skill
Pattern to capture: <description>
```

## Exit state

```bash
git add .claude/skills/ skills/
git commit -m "feat(skills): extract <pattern-name> from session <date>"
```

## Integration

```
/superloopflow:review "code review"
  ↓ (all clean)
/superloopflow:evolve "extract patterns from this session"
  ↓
skills/INDEX.md updated
git commit "feat(skills): extract ..."
  ↓
continuous_learning_done = true
```