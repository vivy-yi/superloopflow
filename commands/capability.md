---
name: capability
description: Look up a specific capability and its providers (v0.4 — read-only)
---

# /superloopflow:capability (v0.4)

Look up a specific capability and list all plugins that provide it.

## Usage

```
/superloopflow:capability <name>
```

## Example

```
/superloopflow:capability code-review
```

## What it does

1. Read `.loop-state/capability-registry.yaml` (built by `/superloopflow:discover`)
2. Filter entries matching the queried name
3. Display providers (plugin + command/skill)
4. If multiple, prompt user to choose

## Output format

```
=== Capability: code-review ===

Providers (3):
  1. ecc (v2.0.0+)        [command]   /ecc:code-review
  2. ecc (v2.0.0+)        [skill]     code-review (use via /superloopflow:review)
  3. superpowers (v6.0.3+) [skill]     verification-before-completion

Bridged as: /superloopflow:review
```
