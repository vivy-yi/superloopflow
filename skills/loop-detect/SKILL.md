---
name: loop-detect
description: How to perform Phase 0.5 tools inventory — detect agentic tools, create .tools-inventory.yaml, run diagnostics
---

# Loop Detect (v3.0)

Phase 0.5: Tools Inventory. After Phase 0 (git initialized), before any code work.

## Purpose

Without explicit declaration, agents can't reason about "which tool is best for this task." Tooling becomes implicit tribal knowledge.

> **This is first-class.** Phase 0.5 treats agentic tools the same way Skills are treated — as versioned, documented first-class entities.

## When to Run

**Trigger:** `git_initialized == True AND tools_inventory_exists == False`

This is the **first phase after bootstrap**, before any code work begins.

## The Three Steps

### Step 1: Run Diagnostic Checks

```bash
# OMC self-check
/omc:omc-doctor

# ECC self-check
/ecc:harness-audit
```

Both commands produce a JSON report of:
- Installed version
- Missing dependencies
- Version conflicts

### Step 2: Document Project Toolchain

Create `.tools-inventory.yaml` at project root:

```yaml
# .tools-inventory.yaml — first-class declaration of agentic tools
agentic_tools:
  omc:
    version: "4.4.0+"
    required: true
    purpose: "Multi-agent orchestration"
    fallback: "manual team mode if OMC unavailable"
  ecc:
    version: "2.0.0+"
    required: true
    purpose: "Quality + skills library"
    fallback: "manual TDD workflow if ECC unavailable"
  superpowers:
    version: "6.0.3+"
    required: true
    purpose: "Process discipline (brainstorming, plans, TDD)"
    fallback: "skip Phase 1/3 if superpowers unavailable"
  planning-with-files:
    version: "1.0.0+"
    required: false
    purpose: "PRD authoring"
    fallback: "manual spec writing"

language_tools:
  python:
    version: "3.11+"
    required: true
  node:
    version: "20+"
    required: false

infrastructure_tools:
  ffmpeg:
    version: "6+"
    required: false
  docker:
    version: "24+"
    required: false
```

### Step 3: Pin Tool Versions

Update `requirements.txt` / `pyproject.toml` / `package.json` to pin agentic tool CLI versions:

```toml
# pyproject.toml (example)
[project.optional-dependencies]
dev = [
    "anthropic>=0.40.0",  # SDK pin
]
```

### Step 4: Set Up Upgrade Alerts

Add to `.github/dependabot.yml` or equivalent:

```yaml
# .github/dependabot.yml
version: 2
updates:
  - package-ecosystem: "pip"
    directory: "/"
    schedule:
      interval: "weekly"
  - package-ecosystem: "npm"
    directory: "/"
    schedule:
      interval: "weekly"
```

## State Update

After completion:

```bash
yq -i '.tools_inventory_exists = true' .loop-state/state.yaml
```

## Exit State

- `.tools-inventory.yaml` exists
- Pinned versions in requirements
- Upgrade alerts configured

## Fallback Strategies

For each tool, define a fallback:

| Tool | Fallback | When to Use |
|------|----------|-------------|
| OMC unavailable | Manual team mode | Small projects, solo dev |
| ECC unavailable | Skip Phase 6 review | Prototype, throwaway code |
| Superpowers unavailable | Skip Phase 1/3 | Existing project, no brainstorming needed |
| Language tool missing | Error + install instructions | Required tool not found |

## Why This Matters

Without `.tools-inventory.yaml`:

❌ "Which tool should I use for this task?"
❌ "Is this tool version compatible?"
❌ "What fallback do I use if X is unavailable?"

With `.tools-inventory.yaml`:

✅ Agents can reason about tool selection
✅ Version conflicts are visible
✅ Fallbacks are explicit
✅ Upgrades are tracked