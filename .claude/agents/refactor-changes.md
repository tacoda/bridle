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

Spot refactoring opportunities in the current diff against `main`. Read-only — do not edit.

## What to Look For

1. **Duplication crossing the rule of three** — three or more commands sharing the same instruction block.
2. **Sections doing more than one thing** — split or rename.
3. **Names that don't reveal intent** — propose better names.
4. **Inconsistent patterns with surrounding files** — frontmatter fields, heading structure, tone.
5. **Long enumerations** — if a command lists more than ~7 numbered steps, ask whether the goal can be described instead.
6. **Dead branches and dead variables** — placeholders no command references; conditional sections never reached.
7. **Comments or notes describing *what*, not *why***.

## Output Format

Numbered list. For each finding:
- **File:line**
- **Smell**
- **Suggested refactor**
- **Rationale**

End with one sentence: which one or two changes would have the highest leverage.
