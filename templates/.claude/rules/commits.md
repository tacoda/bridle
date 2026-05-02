---
description: Commit and pull request rules for [PROJECT_NAME]
---

# Commits & Pull Requests

## Conventional Commits

Format: `<type>(<scope>): <subject>`

Common types:
- `feat` — new feature
- `fix` — bug fix
- `refactor` — behavior-preserving structural change
- `test` — adding or improving tests
- `docs` — documentation only
- `chore` — tooling, dependencies, non-code changes

## Rules

- One logical change per commit. Separate **structural** (refactor) and **behavioral** (feat/fix) changes into different commits.
- The codebase stays releasable on `[MAIN_BRANCH]` at all times. Use feature flags to decouple deploy from release.
- Pre-commit checks must pass before commit. CI is a backstop, not a substitute.
- Do **not** add co-authors to commit messages.
- Do **not** mention the AI agent in commit messages, PR descriptions, or [TASK_TRACKER] tickets.

## Pull Requests

- Title is short (under 70 characters); details go in the body.
- Body links to the [TASK_TRACKER] ticket and explains the **why**.
- Include a test plan when the change touches user-facing behavior.

## Project-Specific Notes

[COMMIT_NOTES]
