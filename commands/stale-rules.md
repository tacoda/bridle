---
description: Flag rules that have not been referenced or updated recently and propose actions
argument-hint: "[staleness threshold in days, default 90]"
---

Find rules in `.claude/rules/` that may be stale and propose what to do with each. Read-only.

`$ARGUMENTS` is the staleness threshold in days (default `90`).

## Steps

1. **Inventory rules.** List `.claude/rules/*.md`. For each:
   - **Last modified** — `git log -1 --format=%at -- <file>` (epoch seconds; convert to days ago)
   - **Reference count** — how many other files in `.claude/` mention the rule by filename
   - **Path scope** — `paths:` from frontmatter, or "always"

2. **Classify each rule:**
   - **stale** — older than the threshold AND zero references
   - **possibly stale** — older than the threshold OR zero references
   - **fresh** — within the threshold and referenced

3. **For stale rules, check whether the pattern still applies.** Sample-grep the rule's body for distinctive phrases against the current codebase. If no matches, the pattern may have been refactored away.

4. **Propose an action per stale rule:**
   - **keep** — still relevant, just hasn't needed updates
   - **refresh** — pattern still applies, but the rule's wording is dated
   - **archive** — move to `.claude/rules/archive/` (out of auto-load) but preserve history
   - **delete** — pattern no longer applies anywhere

5. **Render a table:**

   | Rule | Last modified | References | Pattern still applies? | Suggested action |
   |---|---|---|---|---|

6. **Ask the user which to action.** For each accepted action, perform it (with the safety contract — confirm before delete or archive).

## Rules

- Read-only by default. Edits, archives, and deletes only after explicit user approval per rule.
- Never bulk-archive or bulk-delete. One rule at a time.
- An archived rule keeps git history; a deleted rule does not. Default to archive when in doubt.
