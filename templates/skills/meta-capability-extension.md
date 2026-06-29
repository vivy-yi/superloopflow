---
name: meta-capability-extension
description: How to add a new tool, provider, selector, or pipeline to the project. Use when extending the project's executable surface.
---

# Meta Capability Extension

## When to Use
- Adding a new tool to the BaseTool registry
- Onboarding a new LLM provider
- Adding a new image / video / audio / voice provider
- Adding a new selector strategy
- Adding a new pipeline
- Adding a new stage to an existing pipeline

## Process

### Adding a Tool
1. Create `<project>/tools/<category>/<tool_name>.py`
2. Inherit from `BaseTool`
3. Implement `name`, `description`, `input_schema`, `output_schema`, `run()`
4. Register in `<project>/tools/registry.py` (or autodiscover via decorator)
5. Add to `.tools-inventory.yaml` if it's external
6. Write unit + integration tests
7. Commit: `feat(tools): add <tool-name>`

### Adding a Provider
1. Create `<project>/tools/providers/<provider>.py`
2. Implement the provider protocol (auth, request, response, error mapping)
3. Register in provider registry
4. Add selector strategy if multiple options exist
5. Add to `.tools-inventory.yaml`
6. Write contract tests
7. Commit: `feat(providers): add <provider>`

### Adding a Pipeline
1. Author `pipeline_defs/<pipeline>.yaml` from the manifest template
2. For each stage, create `skills/pipelines/<pipeline>/<stage>-director.md`
3. Add the pipeline to `AGENT_GUIDE.md` > "Pipeline Manifests" section
4. Add skills to `skills/INDEX.md` > `pipelines/<pipeline>/`
5. Smoke-test the full pipeline on a small input
6. Commit: `feat(pipeline): add <pipeline>` + `chore(skills): add <pipeline> directors`

### Adding a Stage
1. Author `skills/pipelines/<pipeline>/<stage>-director.md`
2. Add the stage to `pipeline_defs/<pipeline>.yaml` > `stages[]`
3. Add success_criteria, review_focus, tools_available
4. Test in isolation
5. Commit: `feat(pipeline): add <stage> to <pipeline>`

## Self-Evaluation
- [ ] Does the new code follow immutability + error-handling rules?
- [ ] Are tests written first and passing?
- [ ] Is the new capability registered (tool, provider, manifest, index)?
- [ ] Is the inventory updated if external?
- [ ] Is AGENT_GUIDE.md updated if user-facing?
- [ ] Does the commit message follow conventional format?
