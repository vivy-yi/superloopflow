---
name: init
description: Bootstrap a new project — git init, README, .gitignore, .env.example, Makefile
---

# /superloopflow:init

Bootstrap a new project with the minimum files needed for AI-assisted development.

## Steps

1. Run `git init`
2. Create `README.md` with project name and one-paragraph description
3. Create `.gitignore` for the project's primary language (detect via file extensions)
4. Create `.env.example` (empty template, user fills in API keys)
5. Create `Makefile` with `setup / test / preflight / lint` targets
6. Commit: `chore: initialize project`

## Usage

```
/superloopflow:init
```

## Exit state

- `.git/` initialized
- `README.md` created
- `.gitignore` created
- `.env.example` created
- `Makefile` created
- `git_initialized = true`
- `readme_exists = true`
