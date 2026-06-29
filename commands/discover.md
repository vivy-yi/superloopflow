---
name: discover
description: Read-only discovery of all installed plugins and their capabilities (v0.4 — no manifest protocol)
---

# /loop:discover (v0.4)

**Read-only** auto-discovery of all installed Claude Code plugins and their capabilities. **No writes** to any third-party plugin directory.

## What it does

1. Read `~/.claude/settings.json` to get all enabled plugins
2. For each enabled plugin, walk `~/.claude/plugins/cache/<marketplace>/<plugin>/<version>/`
3. Read:
   - `.claude-plugin/plugin.json` (name, version, description)
   - `commands/*.md` frontmatter (name + description)
   - `skills/*/SKILL.md` frontmatter (name + description)
4. **Infer** LOOP phase from keywords in command/skill names
5. Build `.loop-state/capability-registry.yaml` (in PROJECT directory, not in any plugin)
6. Print a unified cheatsheet

## Implementation (v0.4 — pure read-only)

```python
# Pseudocode of what /loop:discover does internally
import json, re
from pathlib import Path
import yaml

settings = json.loads(Path.home() / ".claude/settings.json").read_text())
enabled = settings.get("enabledPlugins", {})

KEYWORDS = {  # word → LOOP phase
    "brainstorm": 1, "interview": 1, "requirements": 1,
    "spec": 2, "prd": 2, "doc-coauthor": 2,
    "plan": 3, "writing-plans": 3, "decompose": 3, "epic": 3,
    "implement": 5, "autopilot": 5, "ralph": 5, "team": 5, "tdd": 5, "code": 5, "build": 5, "fix": 5,
    "verify": 6, "verification": 6, "checkpoint": 6, "test": 6, "qa": 6, "review": 6, "e2e": 6,
    "learn": 7, "evolve": 7, "skillify": 7, "continuous": 7, "instinct": 7,
    "audit": 0, "harness": 0, "cost": 0, "init": 0, "bootstrap": 0,
}

def infer_phase(text):
    text_lower = text.lower()
    matches = [p for kw, p in KEYWORDS.items() if kw in text_lower]
    return max(set(matches), key=matches.count) if matches else None

registry = {}
for plugin_id, enabled_val in enabled.items():
    if not enabled_val or "@" not in plugin_id:
        continue
    name, marketplace = plugin_id.split("@", 1)
    cache = Path.home() / ".claude/plugins/cache" / marketplace / name
    if not cache.exists():
        continue
    versions = [d for d in cache.iterdir() if d.is_dir()]
    if not versions:
        continue
    latest = max(versions, key=lambda d: d.name)

    # Read plugin.json
    pj = latest / ".claude-plugin" / "plugin.json"
    if not pj.exists():
        continue
    pdata = json.loads(pj.read_text())

    # Read commands
    for cmd in (latest / "commands").glob("*.md") if (latest / "commands").exists() else []:
        content = cmd.read_text()
        fm = re.search(r"^---\n(.*?)\n---", content, re.DOTALL)
        if fm:
            try:
                meta = yaml.safe_load(fm.group(1))
                phase = infer_phase(meta.get("name", "") + " " + meta.get("description", ""))
                if phase is not None:
                    key = meta.get("name", cmd.stem)
                    registry.setdefault(key, []).append({"plugin": name, "phase": phase, "type": "command"})
            except: pass

    # Read skills (similar)
    ...

# Write registry (in PROJECT directory, not in any plugin)
Path(".loop-state/capability-registry.yaml").write_text(yaml.safe_dump({"capabilities": registry}))
```

## Output

```
=== LOOP Capability Registry ===

Phase 0 (Bootstrap):
  - harness-audit         (everything-claude-code)  /ecc:harness-audit
  - cost-report           (everything-claude-code)  /ecc:cost-report
  - harness               (omc)                     /omc-doctor
  ...

Phase 1 (Requirements):
  - brainstorming         (superpowers)            [skill]
  - deep-interview        (omc)                     /omc:deep-interview
  ...

Total: 22 plugins, 214 capabilities, 7/8 phases covered
```

## What this does NOT do

- ❌ Does NOT write to any third-party plugin directory
- ❌ Does NOT add or modify files in `~/.claude/plugins/`
- ❌ Does NOT assume plugin authors added any manifest

## What this DOES

- ✅ Read-only inspection of plugin.json, commands/, skills/
- ✅ Infers capabilities from frontmatter (name + description)
- ✅ Builds project-local capability registry (in your project, not in plugins)

## Usage

```
/loop:discover
```
