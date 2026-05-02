---
description: Review the diff for instruction errors, ambiguity, and goal alignment
allowed_tools:
  - Read
  - Grep
  - Glob
  - "Bash(git diff*)"
  - "Bash(git log*)"
  - "Bash(git show*)"
---

Functional review of the current branch against `main`. Read-only.

## What to Check

1. **Instruction errors** — Steps Claude could not actually perform; missing prerequisites; tool references that won't resolve.
2. **Ambiguity** — Phrases that admit two reasonable interpretations. Pick one or call it out.
3. **Path correctness** — `${CLAUDE_PLUGIN_ROOT}` used where needed; relative paths only where they resolve correctly inside the plugin cache.
4. **Argument handling** — Does `$ARGUMENTS` / `$1` flow match the `argument-hint` frontmatter?
5. **Goal alignment** — Does the change deliver what the task asked for?
6. **Failure handling** — When a step can't complete (file missing, command not installed), is the fallback clear?

## Output Format

Numbered list of findings:
- **File:line**
- **Severity**: `blocker` | `major` | `minor`
- **Finding**
- **Suggested fix**
