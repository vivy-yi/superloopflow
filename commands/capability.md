---
name: capability
description: Look up a specific capability and its providers (v0.4 — read-only)
---

# /loop:capability (v0.4)

Look up a specific capability and list all plugins that provide it.

## Usage

```
/loop:capability <name>
```

## Example

```
/loop:capability code-review
```

## What it does

1. Read `.loop-state/capability-registry.yaml` (built by `/loop:discover`)
2. Filter entries matching the queried name
3. Display providers (plugin + command/skill)
4. If multiple, prompt user to choose

## Output format

```
=== Capability: code-review ===

Providers (3):
  1. ecc (v2.0.0+)        [command]   /ecc:code-review
  2. ecc (v2.0.0+)        [skill]     code-review (use via /loop:review)
  3. superpowers (v6.0.3+) [skill]     verification-before-completion

Bridged as: /loop:review
```
