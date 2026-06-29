---
name: detect-tools
description: Phase 0.5 — Detect installed agentic tools, create .tools-inventory.yaml, run omc-doctor and harness-audit
---

# /superloopflow:detect-tools

Phase 0.5: Tools Inventory. Detect installed agentic tools, create `.tools-inventory.yaml`, run diagnostic checks.

## What it does

1. Run `/omc:omc-doctor` — OMC self-check
2. Run `/ecc:harness-audit` — ECC self-check
3. Detect installed plugins from `~/.claude/settings.json`
4. Create `.tools-inventory.yaml` at project root
5. Pin tool versions if possible
6. Set up upgrade alerts (dependabot)

## Why Phase 0.5 Exists

Without explicit declaration, agents can't reason about "which tool is best for this task." Tooling becomes implicit tribal knowledge.

> "The `.tools-inventory.yaml` is the 'agent skill catalog' for the project."

## Implementation

```bash
# 1. Run OMC self-check
/omc:omc-doctor

# 2. Run ECC self-check
/ecc:harness-audit

# 3. Read enabled plugins from settings.json
cat ~/.claude/settings.json | jq '.enabledPlugins | keys'

# 4. Create .tools-inventory.yaml
cat > .tools-inventory.yaml <<'EOF'
# .tools-inventory.yaml — first-class declaration of agentic tools
agentic_tools:
  omc:
    version: "<detected>"
    required: true
    purpose: "Multi-agent orchestration"
    fallback: "manual team mode if OMC unavailable"
  ecc:
    version: "<detected>"
    required: true
    purpose: "Quality + skills library"
    fallback: "manual TDD workflow if ECC unavailable"
  superpowers:
    version: "<detected>"
    required: true
    purpose: "Process discipline (brainstorming, plans, TDD)"
    fallback: "skip Phase 1/3 if superpowers unavailable"

language_tools:
  python:
    version: "<detected>"
    required: <based on project>
  node:
    version: "<detected>"
    required: false

infrastructure_tools:
  docker:
    version: "<detected>"
    required: false
EOF

# 5. Set up dependabot for weekly updates
mkdir -p .github && cat > .github/dependabot.yml <<'EOF'
version: 2
updates:
  - package-ecosystem: "pip"
    directory: "/"
    schedule:
      interval: "weekly"
EOF
```

## Exit State

- `.tools-inventory.yaml` exists
- OMC doctor passed
- ECC harness-audit passed
- Upgrade alerts configured

## Usage

```
/superloopflow:detect-tools
```