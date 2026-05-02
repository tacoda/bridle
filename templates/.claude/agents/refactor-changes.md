---
description: Identify simplification and pattern-consistency opportunities in the diff
allowed_tools:
  - Read
  - Grep
  - Glob
  - "Bash(git diff*)"
  - "Bash(git log*)"
  - "Bash(git show*)"
---

Spot refactoring opportunities in the current diff against `[MAIN_BRANCH]`. Read-only — do not edit.

## What to Look For

1. **Duplication crossing the rule of three** — extract a shared helper
2. **Methods doing more than one thing** — split or rename
3. **Names that don't reveal intent** — propose better names
4. **Inconsistent patterns with the surrounding code** — align with the established style
5. **Long parameter lists** — consider grouping into a value object
6. **Dead branches and dead variables**
7. **Comments that describe *what*, not *why*** — flag for deletion or rewrite

## Output Format

Numbered list. For each finding:
- **File:line**
- **Smell**
- **Suggested refactor**
- **Rationale**

End with a single sentence: which one or two changes would have the highest leverage.
