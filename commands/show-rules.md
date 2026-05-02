---
description: List every rule in .claude/rules/ with its scope and last-modified date
---

List the rules in this project's harness.

## Steps

1. List `.claude/rules/*.md`. If `.claude/rules/` does not exist, suggest `/bridle:generate-harness` and stop.

2. For each rule, read the frontmatter and extract:
   - **Filename**
   - **Description** (the `description:` field)
   - **Scope** — globs from `paths:` if present, otherwise `always`
   - **Last modified** — `git log -1 --format=%ar -- <file>` (e.g., `3 weeks ago`)

3. Render a single markdown table:

   | Rule | Description | Scope | Last modified |
   |---|---|---|---|

4. After the table, summarize:
   - Total rules
   - Always-loaded
   - Path-scoped
   - Unchanged for 90+ days (candidates for `/bridle:stale-rules`)

## Rules

- Read-only. Do not edit any rule files.
