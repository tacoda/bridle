---
description: Analyze recent commits for recurring patterns that should become rules
argument-hint: "[number of commits to analyze, default 50]"
---

Walk recent commits looking for patterns that should be encoded as rules. Read-only.

`$ARGUMENTS` is the number of commits to scan (default `50`).

## Steps

1. **Pull the recent history.**
   ```
   git log --oneline -n <N> 
   git log --no-merges -n <N> --format="%s%n%b%n---"
   ```

2. **Look for signals.** Cluster commits by:
   - **Recurring fixes** — multiple `fix:` commits touching the same area or fixing the same shape of bug.
   - **Recurring refactors** — multiple `refactor:` commits applying the same transformation.
   - **Review keywords** — commit bodies mentioning "review", "feedback", "rejected", "rework".
   - **Reverts** — `revert` commits, especially clusters.

3. **Cross-reference rules.** For each cluster, check `.claude/rules/` — is the lesson already encoded? If not, this is a candidate.

4. **Propose rules.** For each cluster, present:
   - **Pattern** — one sentence describing what kept happening
   - **Evidence** — the commits (SHA + subject)
   - **Suggested rule** — either "extend `<existing-rule>`" or "new rule `<topic>`"
   - **Confidence** — `high` (3+ commits) | `medium` (2 commits) | `low` (1 commit + intuition)

5. **Ask the user which to action.** For each accepted item, hand off to `/bridle:learn` or `/bridle:add-rule`.

## Rules

- Read-only. Do not write rules directly — propose, then delegate.
- Skip clusters with confidence `low` unless the user asks. Noise hurts the flywheel more than missing one signal.
- Don't propose rules that contradict existing ones without flagging the contradiction explicitly.
