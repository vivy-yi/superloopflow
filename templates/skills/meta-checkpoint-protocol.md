---
name: meta-checkpoint-protocol
description: How to present work for human approval at checkpoint stages. Use when a pipeline stage has human_approval_default: true.
---

# Meta Checkpoint Protocol

## When to Use
- A pipeline stage has `human_approval_default: true`
- You've completed a stage and the manifest requires sign-off before advancing
- The user explicitly asks for a checkpoint

## Process

1. **Stop work at the gate.** Don't auto-advance.
2. **Summarize what was produced:**
   - Stage name
   - Artifact(s) produced (paths, sizes)
   - Time / cost / token spent
   - Number of revision rounds
3. **State success-criteria status:**
   - For each criterion: met / partially-met / not-met + evidence
4. **List open questions / risks:**
   - Things the user should know before approving
5. **Make the decision request explicit:**
   - "Approve and continue to <next-stage>" OR
   - "Revise stage <name> with the following changes..." OR
   - "Hold — I have a question about <X>"
6. **Wait for response.** Do not proceed without explicit approval.

## Output Format

```
## Checkpoint: <stage-name>

### Produced
- <artifact>: <path>, <size>
- <artifact>: <path>, <size>

### Spend
- Wall time: <Xm>
- Cost: <$X.XX>
- Tokens: <N>
- Revision rounds: <N>

### Success Criteria
- [x] <criterion 1> — <evidence>
- [x] <criterion 2> — <evidence>
- [ ] <criterion 3> — <why not met>

### Open Questions / Risks
- <question or risk>

### Decision Requested
- [ ] Approve → continue to <next-stage>
- [ ] Revise <stage-name> with: <changes>
- [ ] Hold — question about <X>
```

## Self-Evaluation
- [ ] Did I stop and not auto-advance?
- [ ] Is the summary complete (artifacts, spend, criteria, risks)?
- [ ] Is the decision request explicit (3 options, not open-ended)?
- [ ] Did I wait for the user's response?
