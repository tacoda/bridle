---
description: Fan out every review agent in .claude/agents/ on the current diff and combine findings
---

Spawn every review agent in `.claude/agents/` against the current diff in parallel and consolidate findings.

## Steps

1. **Inventory review agents.** List `.claude/agents/*.md` and select those whose filename starts with `review-` or equals `self-review` or `refactor-changes`. (Other agents like `ci-diagnose` are not reviewers.)

2. **Spawn them in parallel.** Issue all subagent calls in a single batched call so they run concurrently. Each subagent receives the diff against the project's main branch.

3. **Combine findings.** Merge each agent's numbered list into a single ordered list:
   - Group by severity (`blocker` → `major` → `minor`).
   - Tag each finding with the agent name in parentheses.
   - Deduplicate items multiple agents raised — keep the highest-severity tag.

4. **Render output:**
   - Table: which agents ran, how many findings each produced, runtime.
   - Combined findings list.
   - One-sentence verdict.

## Rules

- Read-only. Do not edit any files based on findings.
- If no review agents exist in `.claude/agents/`, suggest `/bridle:generate-harness` to scaffold them and stop.
- If only one review agent is present, run it directly and note that fan-out had nothing to fan out to.
