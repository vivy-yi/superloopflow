---
name: plugin-bridge
description: How to bridge from /superloopflow:* commands to other plugins using READ-ONLY discovery (v3.0)
---

# Plugin Bridge (v3.0)

How `/superloopflow:*` commands delegate to other plugins via **read-only discovery**.

## Core Principle: NEVER Modify Third-Party Plugin Directories

v0.3 used a "Capability Manifest Protocol" that wrote `manifest.yaml` into other plugins' directories. This was **wrong**:
- Plugin authors didn't authorize the writes
- Plugin upgrades overwrite our changes
- The approach is invasive and fragile

**v0.4+ fix:** Read-only discovery. We never write to `~/.claude/plugins/`. We only:
- Read `~/.claude/settings.json`
- Read `plugin.json`, `commands/*.md`, `skills/*/SKILL.md` (all provided by plugin authors)
- Write to `.loop-state/` in YOUR project

## The Discovery Algorithm

```python
def discover_plugins():
    # 1. Read enabled plugins from user's config
    settings = json.loads(Path.home() / '.claude/settings.json')
    enabled = settings.get('enabledPlugins', {})

    # 2. For each enabled plugin, read its manifest
    for plugin_id in enabled:
        plugin_path = resolve_plugin_path(plugin_id)

        # Read plugin metadata
        plugin_json = json.loads((plugin_path / 'plugin.json').read_text())

        # Read commands
        for cmd_file in (plugin_path / 'commands').glob('*.md'):
            cmd = parse_frontmatter(cmd_file)
            capabilities[cmd.name] = {
                'provider': plugin_id,
                'type': 'command',
                'description': cmd.description
            }

        # Read skills
        for skill_dir in (plugin_path / 'skills').glob('*/SKILL.md'):
            skill = parse_frontmatter(skill_dir)
            capabilities[skill.name] = {
                'provider': plugin_id,
                'type': 'skill',
                'description': skill.description
            }

    # 3. Write catalog to project (NOT to plugin directories)
    write_yaml('.loop-state/capability-registry.yaml', capabilities)
```

## Keyword → Phase Mapping

```python
KEYWORD_TO_PHASE = {
    # Phase 1
    "brainstorm": 1, "interview": 1, "requirements": 1, "clarify": 1,
    # Phase 2
    "spec": 2, "prd": 2, "doc-coauthor": 2,
    # Phase 3
    "plan": 3, "writing-plans": 3, "decompose": 3, "epic": 3,
    # Phase 5
    "implement": 5, "autopilot": 5, "ralph": 5, "team": 5,
    "tdd": 5, "code": 5, "build": 5, "fix": 5, "execution": 5,
    # Phase 6
    "verify": 6, "verification": 6, "checkpoint": 6,
    "test": 6, "qa": 6, "review": 6, "e2e": 6,
    # Phase 7
    "learn": 7, "evolve": 7, "skillify": 7, "continuous": 7, "instinct": 7,
    # Phase 0/0.5
    "audit": 0, "harness": 0, "cost": 0, "init": 0, "bootstrap": 0,
    "detect": 0.5, "tools": 0.5, "inventory": 0.5,
}
```

## When to Add a New Bridge

Add a new `/superloopflow:*` bridge command when:
- A LOOP phase is missing a unified entry point
- Users are confused about which plugin to use
- Multiple plugins provide similar capabilities

## Bridge Anti-Patterns

❌ **Don't write to third-party plugin directories** (v0.3 mistake)

❌ **Don't re-implement the underlying command** — just call it

❌ **Don't hardcode plugin names** — use capability lookup

❌ **Don't hide errors** — let the underlying command's output surface

## Example: /superloopflow:brainstorm Bridge

```bash
# 1. Read capability registry
CAPABILITY=$(cat .loop-state/capability-registry.yaml | yq '.brainstorming')

# 2. If multiple providers, ask user
if [ $(echo "$CAPABILITY" | yq 'length') -gt 1 ]; then
    echo "Multiple providers found:"
    echo "$CAPABILITY" | yq 'keys'
    read -p "Which to use? " choice
fi

# 3. Invoke the provider
PROVIDER=$(echo "$CAPABILITY" | yq '.[0].provider')
TYPE=$(echo "$CAPABILITY" | yq '.[0].type')

case $TYPE in
    command)
        /${PROVIDER}:${COMMAND} "$@"
        ;;
    skill)
        use the ${SKILL} skill
        ;;
esac
```

## State File Location

All state is written to `.loop-state/` in the **project directory**, NOT in plugin directories:

```
project/
├── .loop-state/
│   ├── state.yaml              # Loop state machine state
│   ├── capability-registry.yaml # Discovered capabilities
│   └── logs/                   # Execution logs
├── agents/                     # Agent definitions (project-owned)
├── skills/                     # Project-specific skills (project-owned)
├── .claude/                    # Project rules (project-owned)
└── pipeline_defs/              # Pipeline manifests (project-owned)
```

## Plugin Lifecycle

1. **Installation** — via `/plugin install` or local marketplace
2. **Registration** — in `~/.claude/settings.json` enabledPlugins
3. **Discovery** — by superloopflow's `/superloopflow:discover`
4. **Use** — by `/superloopflow:brainstorm`, `/superloopflow:implement`, etc.
5. **Upgrade** — plugin author updates code
6. **Re-discovery** — re-run `/superloopflow:discover`

## How to Add a New Plugin (Zero-Code)

1. Install: `/plugin install <plugin>`
2. Re-run: `/superloopflow:discover` (now sees the new plugin)
3. Bridge commands automatically use it (if it provides a needed capability)

No need to modify superloopflow. No need to write a manifest.