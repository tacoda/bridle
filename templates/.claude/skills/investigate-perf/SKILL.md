---
name: investigate-perf
description: Investigate a performance problem — pick a metric, baseline it, profile, change one thing, measure delta. No perf claims without before/after numbers.
argument-hint: "[description of the perf problem]"
---

Investigate a performance problem in **{{ PROJECT_NAME }}**: $ARGUMENTS

If `$ARGUMENTS` is empty, ask what is slow and how the user knows. "Feels slow" is not a starting point — needs a metric (latency, throughput, memory, CPU) and a target.

This skill honors `.claude/rules/bridle-mode.md`. In **paired**, ask after each phase. In **solo**, skip routine asks; stop and report when the profile is inconclusive or the optimization trades off against another metric. In **autopilot**, run end-to-end and present numbers + diff at final review.

## IRON LAW

**NO PERFORMANCE CLAIM WITHOUT BEFORE/AFTER NUMBERS IN THIS TURN.**

"Should be faster" / "feels snappier" / "the algorithm is O(n) now" are not evidence. A perf claim requires: a measurement of the *current* state, a measurement of the *changed* state, both produced in this turn, with the same workload, on the same environment. No paired numbers → no claim.

## GOLDEN RULES

- **Aim to measure before you guess.** Hot spots are rarely where you think. A profiler tells you; intuition lies.
- **Aim to change one thing per measurement cycle.** Two changes, one delta — you have learned nothing.
- **Aim for the bottleneck, not the prettiest fix.** Optimizing the 5% path while the 80% path is unchanged is theatre.

## Workflow

### Phase 1: Define the metric
1. Pick the metric: p50/p95/p99 latency, throughput (rps), memory, CPU, query count, payload size.
2. Pick the workload that exercises it. Reproducible. Same every run.
3. Pick the target. "Faster" is not a target. "p95 < 200ms at 100rps" is.
4. Ask for feedback on the metric, workload, and target.

### Phase 2: Baseline
5. Measure the current state under the workload. Record numbers.
6. Capture the environment: project version, dataset, machine, concurrent load.
7. Run 3+ iterations. Note variance — high variance means the workload itself is unstable; fix that first.

### Phase 3: Profile
8. Run an appropriate profiler against the workload (CPU profiler, allocation profiler, query log, network trace).
9. Identify the top contributors by self-time / wall-time / allocations.
10. State the bottleneck hypothesis in one sentence: "The slowness comes from <component> doing <operation> N times."
11. Ask for feedback on the hypothesis before optimizing.

### Phase 4: Change one thing
12. Make the smallest change that addresses the bottleneck.
13. Run `{{ TEST_COMMAND }}` — correctness first. A faster wrong answer is not a win.
14. Re-measure under the same workload. Record numbers.
15. Compute the delta. Report as a table:

    | Metric | Before | After | Δ |
    |---|---|---|---|

16. If the delta is < 5% or in the wrong direction, revert and return to Phase 3 with new evidence.

### Phase 5: Verify generality
17. Run the full benchmark suite (or representative workloads) to confirm the change didn't regress other paths.
18. Check memory / allocations didn't trade off against the wins.

### Phase 6: Review & Commit
19. Show diff + before/after table. Ask for feedback.
20. Spawn `self-review` and `refactor-changes` in parallel.
21. Run `/pre-commit`.
22. Commit as `perf(<scope>): <subject>` with the before/after numbers in the body.

### Phase 7: Document the bottleneck
23. If the optimization touched a non-obvious hot path, add a comment at the call site or a note to a feature doc explaining why this code is shaped the way it is. Future readers will undo the optimization otherwise.

## Anti-Patterns

- **"Optimizing" without a profile.** Pattern-match optimizations are guesses with prejudice.
- **Micro-benchmarks of code that runs once.** Perf budgets live on the hot path; optimize what runs N times, not what runs 1.
- **Aggregating wins across two changes.** "We made it 3x faster" with two unrelated commits hides which change did the work — and which change introduced the eventual regression.
