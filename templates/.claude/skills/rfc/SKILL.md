---
name: rfc
description: Open a Request for Comments — a structured proposal that solicits feedback before a decision is made. Saved to docs/rfcs/NNNN-<slug>.md.
argument-hint: "[short topic of the proposal]"
---

Draft an RFC for **{{ PROJECT_NAME }}**: $ARGUMENTS

If `$ARGUMENTS` is empty, ask the user what they want to propose before starting.

An RFC is the *before* of an ADR. RFCs invite input on an open question. ADRs record a decision that has been made. If the user has already made the call, route them to the `adr` skill instead.

This skill honors `.claude/rules/bridle-mode.md`. In **paired**, ask for feedback on each section. In **solo**, draft the full RFC and stop only when *unresolved questions* are too unresolved to commit to a clear ask. In **autopilot**, draft end-to-end and surface unresolved questions explicitly at final review.

## IRON LAW

**EVERY RFC NAMES THE OPEN QUESTIONS IT EXPECTS REVIEWERS TO ANSWER.**

An RFC without an `Unresolved Questions` section is a memo, not a request for comments. The reader must be able to tell, in 30 seconds, what feedback the author needs. "Tell me what you think" is not a question — name the trade-off, name the alternative, name the constraint that's still in the air.

## GOLDEN RULES

- **Aim for the smallest design that solves the problem, then explore drawbacks honestly.** A perfect-sounding design with no drawbacks is hiding them.
- **Aim to enumerate alternatives, including "do nothing".** The status quo has costs and benefits — name them, don't assume the proposal wins by default.
- **Aim for prior art.** If three other projects have solved this, link them. If no one has, ask why.

## Workflow

### Phase 1: Frame the proposal
1. State the proposal in one sentence: "Add X to do Y" / "Change Z so that W".
2. Identify the next available RFC number by listing `docs/rfcs/`. Numbered `NNNN` (zero-padded, 4 digits) starting at `0001`.
3. Choose a slug. File path: `docs/rfcs/NNNN-<slug>.md`.

### Phase 2: Motivation
4. Write the **Motivation** — the problem, with evidence (tickets, bugs, customer requests, recurring code patterns). Why now? Why does the status quo fall short?
5. Ask for feedback. A weak motivation makes the rest of the RFC moot.

### Phase 3: Detailed design
6. Describe the proposal in enough detail that a reader could implement it without asking the author. Include:
   - API or interface changes (signatures, schemas, contracts).
   - Migration strategy if existing code/data is affected.
   - Examples of usage — show, don't just tell.
   - Compatibility implications (backward, forward, semver).

### Phase 4: Drawbacks
7. List drawbacks honestly. Implementation cost, complexity introduced, ergonomic regressions, performance implications, ongoing maintenance.
8. If you can't think of any drawbacks, you haven't thought hard enough — invite the user to add what you missed.

### Phase 5: Alternatives
9. List alternatives, including "do nothing". For each: how it would work, why it's worse than the proposal.
10. The point is to show the proposal won the comparison — not that the comparison didn't happen.

### Phase 6: Prior art
11. Link to similar designs in other projects, languages, or papers. Note what they got right and where they hit limits.

### Phase 7: Unresolved questions
12. Name the open questions. Specific. Phrased so a reviewer can answer them.
13. Mark which questions block acceptance vs. which can be deferred to implementation.

### Phase 8: Save & circulate
14. Save to `docs/rfcs/NNNN-<slug>.md` using the template below.
15. Show the RFC. Ask for feedback before circulating.
16. Once the user is happy, commit as `docs(rfc): NNNN-<slug>` and push.
17. Suggest opening a discussion thread (PR, GitHub issue, design doc comment) and naming reviewers.

### Phase 9: After review (later session)
18. When discussion settles, the RFC is either **Accepted** (route to the `adr` skill to write an ADR that closes the loop) or **Rejected** (update the RFC status to *Rejected* with the reasoning, leave it in the repo as the record of the path not taken).

## RFC template

```markdown
# RFC NNNN. <Title>

- **Status:** Draft | In Review | Accepted | Rejected | Superseded by RFC NNNN
- **Date:** YYYY-MM-DD
- **Author:** <name>
- **Reviewers:** <names or roles>
- **Discussion:** <link to PR or thread>

## Summary

<One paragraph. What this proposes, in plain language.>

## Motivation

<The problem. Evidence. Why now.>

## Detailed Design

<API, schema, behavior. With examples.>

## Drawbacks

-

## Alternatives

### Alternative A: Do nothing
- Pros:
- Cons:

### Alternative B: <name>
- How it works:
- Why not:

## Prior Art

-

## Unresolved Questions

- [ ] <specific question>
- [ ] <specific question>

## Future Possibilities

<Optional. What this enables down the line that doesn't have to happen now.>
```

## Key Rules

- RFCs are versioned in-repo. Discussion happens elsewhere; the RFC captures the proposal and outcome.
- An accepted RFC is followed by an ADR (`adr` skill) that records the decision and supersedes the RFC's *Unresolved Questions*.
- A rejected RFC is **not deleted** — it's a record of "we considered this and chose not to," which is valuable for future-you.
