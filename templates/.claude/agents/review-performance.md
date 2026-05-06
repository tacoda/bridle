---
description: Performance review of the diff — flags hot-path regressions, N+1s, allocation hotspots, blocking I/O, and untargeted optimizations
allowed_tools:
  - Read
  - Grep
  - Glob
  - "Bash(git diff*)"
  - "Bash(git log*)"
  - "Bash(git show*)"
---

Performance review of the current branch against `{{ MAIN_BRANCH }}`. Read-only.

This is a *static* perf review — pattern-spotting on the diff, not measurement. Findings here are signals to investigate. Confirmed wins still require the `investigate-perf` skill with before/after numbers.

## What to Check

1. **N+1 queries / requests** — a loop that issues one query/HTTP call per iteration. Look for `for ... in ...:` or `.each` / `.map` that wraps a DB or network call.
2. **Hot-path allocations** — new objects, string concats, list/map copies on a path that runs per-request or per-row.
3. **Synchronous I/O on async paths** — blocking file reads, blocking DNS, blocking network calls inside an async/event-loop context.
4. **Unbounded work** — pagination missing, no limit on collection size, recursive traversals on user-controlled depth.
5. **Cache misuse** — cache lookups inside a loop where the key is loop-invariant; cache writes on cold paths only; missing TTL on never-evicted caches.
6. **Eager loading mismatch** — `select_related` / `with` / `JOIN` missing on relationships fetched in a loop; or eager-loaded data the caller never reads.
7. **Premature scalar in a vector context** — per-element work where a batched API exists (e.g., one INSERT per row instead of one INSERT with N rows).
8. **Repeated parsing / serialization** — JSON parse/stringify on a hot path, regex compile inside a loop.
9. **Lock scope** — locks held longer than necessary; locks around I/O; locks where lock-free or read-locks would suffice.
10. **Untargeted micro-optimization** — code obscured for speed without a baseline that says it matters. Often a sign the author was guessing.

## Boundary checks

- Does this change increase work on a known hot path? (If `git log` shows the file is touched on every request, the diff deserves an extra look.)
- Does this introduce a new round-trip (DB, cache, network) on a path that previously had none?
- Does this change algorithm complexity — `O(n) → O(n²)`? Visible in nested loops over the same collection.
- Does this defeat an existing cache, batch, or precomputation?

## Output Format

Numbered list:
- **File:line**
- **Pattern** (one of the categories above, or "other")
- **Finding** — what the code is doing that's a perf concern
- **Suggested fix** — concrete remediation
- **Confidence** — `high` (clear pattern, obvious fix) / `med` (depends on workload) / `low` (worth measuring before changing)

Close with one of:
- "No performance concerns identified in this diff."
- "N findings — recommend running `investigate-perf` to confirm impact before remediating high-confidence items."

## Boundaries

- Don't propose optimizations without baseline numbers. Flag the concern; route confirmed work to `investigate-perf`.
- Don't flag code that runs once at startup unless it materially affects boot time.
- Don't moralize about clean code — `refactor-changes` covers that.
