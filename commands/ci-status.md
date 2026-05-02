---
description: Show the latest CI runs for the current branch via gh
---

Report the latest CI runs for the current branch using `gh`.

## Steps

1. **Detect the branch.** `git branch --show-current`.

2. **List recent runs.**
   ```
   gh run list --branch "$(git branch --show-current)" --limit 10 --json databaseId,name,status,conclusion,createdAt,url
   ```

3. **Render a table:**

   | When | Workflow | Status | Conclusion | URL |
   |---|---|---|---|---|

4. **If the most recent run failed,** offer to spawn the `ci-diagnose` agent to triage. Don't run it automatically.

5. **If `gh` is not authenticated** or the repo has no CI configured, say so plainly and stop. Don't fall back to scraping the web.

## Rules

- Read-only.
- Don't fetch full logs by default — that's `ci-diagnose`'s job.
- If the branch is not pushed yet, say so; there are no runs to show.
