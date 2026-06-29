---
name: verify
description: Bridge to Superpowers verification-before-completion skill — pre-completion checklist
---

# /loop:verify

Bridge to **Superpowers `verification-before-completion`** skill — run pre-completion checklist to catch over-claims.

## What it does

1. Read `.loop-state/capability-registry.yaml`
2. Find Superpowers' `verification-before-completion` skill
3. Invoke the skill with the claim: "I have finished implementing <plan_path>"
4. Run through checklist of common over-claims

## Checklist items

- [ ] All tests pass (not just "I ran tests")
- [ ] Linter passes
- [ ] Build succeeds (if applicable)
- [ ] No TODO/FIXME in committed code
- [ ] Self-review done
- [ ] Code review requested or completed
- [ ] Documentation updated

## When to use

- Before declaring a feature "done"
- Before moving to Phase 7 (continuous learning)
- After `/loop:ralph` fixes tests

## When NOT to use

- During active implementation (wait until you think you're done)
- For quick fixes (single-file edits don't need full verification)

## Bridge target

- **Provider**: Superpowers
- **Skill**: `verification-before-completion`
- **Phase**: 6A (before code review)

## Usage

```bash
/loop:verify "I have finished implementing docs/superpowers/plans/2026-06-29-myfeature-plan.md"
```

## Exit states

| Finding | Severity | Action |
|---------|----------|--------|
| Tests not green | Critical | Fix before declaring done |
| Lint failing | Critical | Fix before declaring done |
| Build failing | Critical | Fix before declaring done |
| TODO/FIXME present | High | Fix or document why deferred |
| No self-review | Medium | Do self-review |
| No code review | Medium | Request review |

## Integration

```
/loop:implement "build the feature"
  ↓
/loop:verify "I finished implementing..."
  ↓ (findings)
/loop:ralph "fix the issues"
  ↓ (verify clean)
/loop:review "code review"
```