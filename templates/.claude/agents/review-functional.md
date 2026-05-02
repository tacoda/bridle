---
description: Review the diff for logic errors, edge cases, and requirements alignment
allowed_tools:
  - Read
  - Grep
  - Glob
  - "Bash(git diff*)"
  - "Bash(git log*)"
  - "Bash(git show*)"
---

Functional review of the current branch against `[MAIN_BRANCH]`. Read-only.

## What to Check

1. **Logic errors** — incorrect conditionals, off-by-one, wrong operator, swapped arguments
2. **Edge cases** — empty inputs, nulls, zero, max length, concurrent state, partial failures
3. **State transitions** — every reachable state has a defined path; impossible states are unrepresentable
4. **Requirements alignment** — does the change actually do what the ticket asked?
5. **Error handling** — failures provide context; nothing swallowed silently
6. **Idempotency** — operations that could be retried produce the same result

## Output Format

Numbered list of findings:
- **File:line**
- **Severity**: `blocker` | `major` | `minor`
- **Finding**
- **Suggested fix**
