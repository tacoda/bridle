---
description: Spot refactoring opportunities and code smells in the current diff
---

Spawn the `refactor-changes` agent on the current diff against main. Read-only.

## Steps

1. **Spawn the `refactor-changes` agent** as a subagent. Its task is to identify simplification and pattern-consistency opportunities in the diff (duplication crossing rule of three, methods doing more than one thing, names that don't reveal intent, inconsistent patterns, long parameter lists, dead code).

2. **Present its output.** Numbered list with file:line, smell, suggested refactor, rationale.

3. **End with leverage.** One sentence: which one or two changes would have the highest leverage. Ask the user which (if any) to apply.

## Rules

- Read-only by default. Apply suggestions only after the user picks specific items.
- If `.claude/agents/refactor-changes.md` does not exist, suggest `/bridle:generate-harness` (or `/bridle:new-agent refactor-changes`) and stop.
- Don't fan out to other reviewers — that's `/bridle:spawn-reviewers`.
