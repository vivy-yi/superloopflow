---
name: meta-doc-author
description: How to author project meta-documentation (entry files, AGENT_GUIDE, rules, skills, pipeline manifests, agent registry). Use when creating or updating project-level documentation that helps AI agents work on the project.
---

# Meta-Doc Author

## When to Use
- Creating new entry files (CLAUDE.md, AGENTS.md, etc.)
- Writing AGENT_GUIDE.md
- Creating project rules
- Creating project skills
- Writing pipeline manifests
- Authoring agent role documents

## Process
1. Identify the meta-doc type (entry / guide / rule / skill / manifest / role)
2. Use the corresponding `/loop:create-*` command
3. Customize the generated file based on project specifics
4. Run tests if applicable
5. Commit with conventional format

## Self-Evaluation
- [ ] Does the doc follow its template?
- [ ] Is it concrete enough for AI agents to use?
- [ ] Are cross-references correct?
- [ ] No placeholders or TODOs?
