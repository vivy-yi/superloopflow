---
name: create-pipeline-manifest
description: Create pipeline_defs/<pipeline>.yaml with stages, skills, review criteria
---

# /superloopflow:create-pipeline-manifest

Implements Phase 4E of LOOP.md — pipeline manifests.

## Steps

1. Read project spec to extract stages
2. For each stage, gather:
   - name
   - skill path (e.g., `pipelines/<name>/<stage>-director.md`)
   - produces (artifact names)
   - tools_available
   - review_focus
   - success_criteria
   - human_approval_default
3. Generate `pipeline_defs/<pipeline>.yaml` from `templates/pipeline_manifest.yaml`
4. Validate against `pipeline_manifest.schema.json`
5. Commit: `feat(pipeline): add <pipeline> manifest`

## Output

`pipeline_defs/<pipeline>.yaml` validated against schema.

## Usage

```
/superloopflow:create-pipeline-manifest
```
