---
description: Verify the current diff — run pre-commit checks and spawn functional + security review agents in parallel
---

Verify the current branch is ready to ship. Combines automated checks and parallel review agents.

## Steps

1. **Run `/bridle:check`.** If it fails, stop and report. No point reviewing code that doesn't lint or pass tests.

2. **If checks pass, spawn review agents in parallel:**
   - `review-functional` — logic errors, edge cases, requirements alignment
   - `review-security` — OWASP Top 10 mapped to the diff
   
   Run both as subagents in a single batched call so they execute concurrently.

3. **Combine findings.** Merge both agents' numbered lists into a single ordered list, grouped by severity (`blocker` → `major` → `minor`). Deduplicate any finding that both agents raised.

4. **Report.** Present:
   - Check results (one line per check)
   - Combined findings (numbered)
   - One-sentence verdict: `ship` / `fix blockers first` / `discuss`

## Rules

- Read-only. Do not edit any files based on findings.
- If `review-security.md` does not exist in `.claude/agents/`, run only `review-functional` and note that security review was skipped.
- Don't synthesize a "fix" — present the findings; the user decides.
