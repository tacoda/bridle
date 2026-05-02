---
description: Audit the entire codebase against every rule in .claude/rules/ and report violations
---

Walk the codebase looking for violations of every rule in `.claude/rules/`. Read-only — produces a report, makes no edits.

## Steps

1. **Inventory rules.** List `.claude/rules/*.md`. For each, read the frontmatter (`description`, `paths`) and the body to understand what the rule prohibits or requires.

2. **Determine scope per rule.**
   - `paths:` present → audit only files matching those globs.
   - `paths:` absent → audit the whole repository.

3. **Audit in parallel.** For each rule, spawn a subagent that scans its scope and lists violations. Batch the spawns into a single call so they run concurrently.

   Each subagent receives:
   - The rule's full text
   - The list of files in its scope
   - Instructions to return a numbered list of violations: `file:line`, the offending pattern, why it violates the rule.

4. **Aggregate.** Collect all subagents' findings into a single table:

   | Rule | File:line | Violation |
   |---|---|---|

5. **Summarize.** After the table:
   - Total violations
   - Top three rules with the most violations
   - One sentence on the highest-leverage cleanup

## Rules

- Read-only. Do not propose specific code edits — that is a follow-up.
- If `.claude/rules/` does not exist or is empty, suggest `/bridle:generate-harness` and stop.
- Skip rules whose body has no enforceable signal (purely philosophical guidance) — note them as "non-auditable" in the summary.
- Cap each rule's scan at a reasonable file count (e.g., 200) to keep runtime bounded; if truncated, say so.
