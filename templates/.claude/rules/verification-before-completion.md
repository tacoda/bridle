# Verification Before Completion

Claiming work is complete without verification is dishonesty, not efficiency.

## IRON LAW

**NO COMPLETION CLAIMS WITHOUT FRESH VERIFICATION EVIDENCE.**

If you have not run the verification command in this turn, you cannot claim it passes. "Should pass" / "I think it works" / "the linter looked happy earlier" are not evidence.

## The gate

Before claiming any status — *passes*, *fixed*, *clean*, *done*, *green*:

1. **Identify** — what command proves this claim?
2. **Run** — execute it, fresh, in full
3. **Read** — full output, exit code, failure count
4. **Verify** — does the output actually confirm the claim?
5. **Then** — make the claim, paired with the evidence

Skip any step → you are guessing, not verifying.

## Claim → required evidence

| Claim | Required evidence |
|---|---|
| Tests pass | `{{ TEST_COMMAND }}` exits 0, 0 failures |
| Linter clean | `{{ LINT_COMMAND }}` exits 0, 0 errors |
| Build succeeds | `{{ BUILD_COMMAND }}` exits 0 |
| Bug fixed | The original-symptom test passes (and previously failed) |
| Regression test works | RED → GREEN cycle observed in this turn |
| Subagent completed | `git diff` shows the changes — not the agent's self-report |
| Requirements met | Line-by-line walk through the spec or plan |

## Common failure modes

- **Stale evidence.** A test run from before the last edit doesn't count. Re-run.
- **Partial evidence.** Linter clean does not imply tests pass. Each claim needs its own command.
- **Inferred evidence.** "The change looks small, so the build is fine" is a guess.
- **Self-reported subagent success.** A subagent says "done" → check the diff yourself.

## Why this matters

A confident-but-false claim is worse than an honest "I don't know yet." The user trusts the next claim less; the work has to be re-verified anyway. Evidence is cheap; trust is not.
