---
name: meta-reviewer
description: How to perform self-review after any stage. Use after completing work on a stage, before declaring done.
---

# Meta Reviewer

## When to Use
- After completing any stage of a pipeline
- Before marking a task done
- Before committing a non-trivial change
- When the user asks for a review

## Process

1. **Pause and step back.** Don't review the same minute you wrote the code.
2. **Re-read the stage's success criteria.** Are they all met?
3. **Walk the artifact(s).** Re-read what you produced as if you were a fresh reviewer.
4. **Look for:**
   - Critical: bugs, security issues, data loss, contract violations
   - Suggestion: better patterns, missing tests, unclear naming
   - Nitpick: formatting, comment phrasing
   - Investigation: things you're not sure about; flag for follow-up
5. **Write the review.** For each finding: severity + location + concrete fix.
6. **Decide:** PASS / REVISE / PASS_WITH_WARNINGS.
   - Max 2 rounds of self-review; escalate to a human if you can't converge.

## Output Format

```
## Review: <stage-name>
Decision: PASS | REVISE | PASS_WITH_WARNINGS

### Critical
- (none)

### Suggestions
- [file:line] <finding> → <fix>

### Nitpicks
- [file:line] <finding> → <fix>

### Investigation
- <open question> — needs <human/agent/experiment>
```

## Self-Evaluation
- [ ] Did I actually re-read the artifact (not just remember writing it)?
- [ ] Did I find at least one issue (perfect scores = rubber stamp)?
- [ ] Are suggestions actionable (concrete fix provided)?
- [ ] Did I respect the 2-round cap?
