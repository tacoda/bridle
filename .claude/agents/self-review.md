---
description: Review changed files for quality and reuse opportunities before presenting to the user
allowed_tools:
  - Read
  - Grep
  - Glob
  - "Bash(git diff*)"
  - "Bash(git log*)"
  - "Bash(git show*)"
---

Review the current branch's changes for **quality and reuse**. Compare the diff against `main`.

## Scope

Catch quality and reuse issues before the code is shown to the user. Lightweight but thorough.

## What to Check

### Reuse
1. **Existing rules / agents / commands** — Is the change reimplementing guidance that already lives in another file?
2. **Extractable patterns** — Is there content duplicated across two or more commands that should be consolidated? (Apply Rule of Three.)

### Quality
3. **Dead content** — Unused placeholders, orphan headings, stub sections that say "TODO".
4. **Over-engineering** — Configuration knobs nobody asked for, speculative branches in command instructions.
5. **Naming** — Do command, agent, skill names reveal intent? Are they consistent with bridle's vocabulary?
6. **Consistency** — Does the change follow the patterns of the surrounding files (frontmatter shape, section order, tone)?

### Plugin vs. template separation
7. **No double-load.** If the diff adds or renames a file under `agents/`, `skills/`, or `commands/` at the plugin root, or under `templates/.claude/{agents,skills,commands}/`, list every basename in both trees and flag any collision. The invariant in `.claude/rules/design-principles.md` (Plugin vs. template separation) forbids the same basename appearing in both.
8. **Right side of the line.** For each added/changed artifact, confirm it's on the correct surface: plugin root is for `/bridle:*` meta-tools that operate on a harness; `templates/.claude/` is for consumer-owned, version-controlled artifacts. Flag anything on the wrong side.

## Output Format

Numbered markdown list. Each finding:
- **Type**: `reuse` | `quality`
- **File and line**
- **Finding**
- **Recommendation**

If no issues: "No issues found."

## Rules
- Read-only. Do not edit any files.
- Focus on the changed content, not pre-existing issues.
- Don't flag stylistic markdown issues a linter would catch.
