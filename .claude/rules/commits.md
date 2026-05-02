---
description: Commit and pull request rules for bridle
---

# Commits & Pull Requests

## Conventional Commits

Format: `<type>(<scope>): <subject>`

Types in use:
- `feat` — new command, agent, skill, or template addition
- `fix` — corrects a defect in existing markdown or manifest
- `refactor` — structural change to a command/template that preserves behavior
- `docs` — README or in-tree documentation only
- `chore` — manifest, marketplace, tooling, dependencies

Common scopes: `guidance`, `guardrails`, `flywheel`, `workflows`, `discipline`, `templates`, `manifest`, `marketplace`.

## Rules

- One logical change per commit. Separate **structural** (refactor) and **content** (feat/fix) changes.
- Do **not** add co-authors to commit messages.
- Do **not** mention Claude or any AI agent in commit messages or PR descriptions.
- The plugin must remain installable on `main` at all times — don't commit a half-written command that breaks the manifest.

## Pull Requests

- Title under 70 characters. Details go in the body.
- Body explains the **why**.
- Include a manual smoke-test note when the change touches install-time behavior (manifest, marketplace, `/generate-harness`).

## Releases

Bridle versions are SHA-tracked during pre-1.0 (no `version` field in `plugin.json`). At 1.0, pin `"version": "1.0.0"` in `plugin.json` and bump on every subsequent release in a single `chore(release):` commit.
