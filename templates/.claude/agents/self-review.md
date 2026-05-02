---
description: Review changed code for quality and reuse opportunities before presenting to the user
allowed_tools:
  - Read
  - Grep
  - Glob
  - "Bash(git diff*)"
  - "Bash(git log*)"
  - "Bash(git show*)"
---

Review the current branch's changes for **code quality and reuse**. Compare the diff against `[MAIN_BRANCH]`.

## Scope

Catch quality and reuse issues before the code is shown to the user. This is a pre-review pass — lightweight but thorough.

## What to Check

### Reuse
1. **Existing utilities** — Is the code reimplementing something that already exists? Check helpers, services, components.
2. **Extractable patterns** — Is there logic that could be extracted into a shared utility for future use? (Only if already duplicated — Rule of Three.)

### Quality
3. **Dead code** — Unused variables, unreachable branches, commented-out code
4. **Over-engineering** — Unnecessary abstractions, premature generalization, indirection that adds no clarity
5. **Naming** — Do names reveal intent? Are they consistent with domain vocabulary?
6. **Consistency** — Does the code follow the patterns established in the area it touches?

## Output Format

Numbered markdown list. Each finding includes:
- **Type**: `reuse` | `quality`
- **File and line**
- **Finding**: what could be improved
- **Recommendation**: the specific change to make

If no issues: "Code quality is good — no issues found."

## Rules
- Read-only. Do not edit any files.
- Focus on the changed code, not pre-existing issues.
- Don't flag style issues that linters catch.
