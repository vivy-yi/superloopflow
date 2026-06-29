---
name: setup
description: One-command setup of the LOOP ecosystem (v0.4 — read-only plugin discovery)
---

# /superloopflow:setup (v0.4)

One-command setup of the LOOP-era development ecosystem. **Read-only** plugin discovery.

## What it does

1. Detect Claude Code installation
2. Read `~/.claude/settings.json` to enumerate enabled plugins
3. **Read-only discovery** of each plugin's `.claude-plugin/plugin.json` + `commands/` + `skills/`
4. Build `.loop-state/capability-registry.yaml` (in PROJECT directory, not in any plugin)
5. Print unified cheatsheet of all available commands + skills
6. Detect capability gaps per LOOP phase

## Implementation (v0.4 — NO writes to third-party plugins)

```bash
# 1. Verify Claude Code
claude --version || echo "Claude Code not installed"

# 2. Build capability registry (read-only)
python -c "
import json, re
from pathlib import Path
import yaml
settings = json.loads(Path.home() / '.claude/settings.json').read_text())
# ... (same as /superloopflow:discover internals) ...
"

# 3. Print cheatsheet
/superloopflow:discover

# 4. Report gaps
echo '⚠️  Phase 4 has no external provider (intentional — flow-orchestrator owns meta-docs)'
echo '✓  Phase 5 has 84+ capabilities from 5+ plugins'
echo '✓  Phase 6 has 20+ verification-related capabilities'
```

## Capability registry

Writes to `.loop-state/capability-registry.yaml` in the **project** directory. **Does NOT** modify any plugin.

## Capability gaps

If a phase has 0 providers, the cheatsheet flags it. **Note**: Phase 4 (meta-docs) intentionally has 0 external providers — flow-orchestrator provides this.

## Usage

```
/superloopflow:setup
```
