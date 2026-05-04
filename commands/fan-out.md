---
description: Fan out one agent in parallel over a set of independent items, combining findings into a single report
argument-hint: "<agent> <items>"
---

Dispatch one agent in parallel over N independent items.

This is the dual of `/bridle:spawn-reviewers` — that fans many agents over one input (multi-lens review of the current diff); this fans one agent over many inputs (same lens, many subjects).

## When to use

Sweet spot: **bulk, independent, read-only or independent-write work** where each item can be analyzed in isolation.

Good fits:
- Per-file security review across a directory.
- Per-rule staleness check.
- Per-failure CI triage when the failures are unrelated.
- Per-comment PR triage.
- Per-route compliance check.

## When NOT to fan out

Sequential work cannot be parallelized. Refuse and recommend a skill or sequential loop instead when:

- The work is a **TDD loop** (red → green → refactor — each step has to see the previous outcome).
- The work is a **multi-phase skill** like `implement-change` or `fix-bug` whose phases depend on each other.
- The work is a **cross-cutting refactor** (one rename touches many files, but the rename itself is one decision).
- The agent needs to **see what previous agents produced** before running.

When in doubt: if items can't truly run in any order without coordination, don't fan out.

## Steps

1. **Parse `$ARGUMENTS`.** First whitespace-separated token is the agent name; the remainder is the item set (glob, comma list, explicit paths, ticket IDs, etc.).

2. **Resolve items.**
   - If items are missing, ask the user to describe the problem and help **decompose** it into a list of independent items. Confirm the list before dispatching.
   - If items contain glob patterns, expand them.
   - Deduplicate.

3. **Resolve the agent.** Look for `.claude/agents/<name>.md`. If absent, list available agents and ask which one the user meant.

4. **Validate before dispatch.**
   - **Item count.** If items < 3, suggest running the agent inline instead — parallelism overhead isn't worth it. Ask whether to proceed anyway.
   - **Sequential agent.** If the agent's `description` mentions "diff" or "the current branch", warn that it's a single-input agent — fanning out makes no sense unless the user explicitly wants per-item diffs.
   - **Interdependent items.** Ask the user if any item's analysis depends on another. If yes, refuse and recommend a single agent invocation with all items as context, or a skill for sequential work.

5. **Dispatch in parallel.** Issue all Agent tool calls in a single batched call so they run concurrently. Each invocation receives one item as its scope.

6. **Combine findings.** Merge each subagent's numbered list into one ordered list:
   - Group by item.
   - Within an item, group by severity (`blocker` → `major` → `minor`) if the agent produces severity tags.
   - Surface findings that recur across items in a separate "patterns across items" section — these are candidates for a `/bridle:learn` rule update.

7. **Render output.**
   - Table: items processed, findings per item, runtime per agent.
   - Combined findings, grouped by item.
   - "Patterns across items" section, if any.
   - One-sentence verdict.

## Rules

- Read-only. Do not edit any files based on findings.
- One agent, many items. For multi-lens review on a single input, use `/bridle:spawn-reviewers` instead.
- Pass items verbatim to the dispatched agent — do not summarize or pre-filter.
- If decomposition reveals the work isn't actually parallelizable, abort and tell the user why.
