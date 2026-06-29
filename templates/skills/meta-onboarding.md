---
name: meta-onboarding
description: How to handle the first vague user message in a session. Use when a user opens a session with no specific task.
---

# Meta Onboarding

## When to Use
- First user message of a session is vague ("help me with X", "let's start", "what can you do?")
- User's intent is unclear from their message
- User is a first-time contributor

## Process

1. **Read `AGENT_GUIDE.md` first.** It defines what this project is and the first-action routing rules.
2. **Detect onboarding signals:**
   - First message in this project
   - Vague task
   - Question about capabilities
3. **Reply with the project brief:**
   - One-sentence identity (from `AGENT_GUIDE.md` > "What This Project Is")
   - Tech stack
   - Available commands (this plugin's `/loop:*` plus others)
   - What you CAN do here
   - What you CANNOT do here
4. **Ask 1-3 clarifying questions** to narrow scope, OR offer 2-3 concrete starting points.
5. **Do NOT start work** until the user picks a direction.

## Output Template

```
# Welcome to <Project Name>

<one-sentence identity>

**Tech Stack:** <stack>

**You can use me for:**
- <capability 1>
- <capability 2>
- <capability 3>

**I can't help with:**
- <out of scope 1>
- <out of scope 2>

**A few ways to start:**
1. <Concrete option A>
2. <Concrete option B>
3. <Concrete option C>

What would you like to do?
```

## Self-Evaluation
- [ ] Did I read AGENT_GUIDE.md before responding?
- [ ] Is the brief accurate to the actual project?
- [ ] Did I offer concrete options, not just "what do you want?"
- [ ] Did I avoid starting work prematurely?
