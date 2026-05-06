---
name: adr
description: Capture an architectural decision as an ADR — context, options, decision, consequences — saved to docs/adrs/NNNN-<slug>.md
argument-hint: "[short topic of the decision]"
---

Record an architectural decision in **{{ PROJECT_NAME }}**: $ARGUMENTS

If `$ARGUMENTS` is empty, ask the user what decision to record before starting.

This skill honors `.claude/rules/bridle-mode.md`. Feedback checkpoints below describe **paired** behavior. In **solo**, skip routine asks; stop and report when the options are not clearly distinguishable or the trade-offs are not yet legible. In **autopilot**, draft each section, present the full ADR at the end, and log assumptions.

## IRON LAW

**NO ARCHITECTURAL REVERSAL WITHOUT A SUPERSEDING ADR.**

An ADR captures *why* a decision was made, with the context the decision-maker had at the time. Reversing or replacing it is an architectural event in its own right — record the new ADR with `Supersedes: NNNN`, never silently rewrite history. The append-only ledger is the point.

## GOLDEN RULES

- **Aim for the smallest decision worth recording.** "Use Postgres over MySQL" is an ADR. "Renamed a variable" is not. The threshold is reversibility cost.
- **Aim for two or more options compared, not one option justified.** A decision with no alternatives is a foregone conclusion, not a decision.
- **Aim for honest consequences.** Every decision has trade-offs. An ADR with only positives is propaganda.

## Workflow

### Phase 1: Frame
1. State the decision in one sentence. ("Adopt X for Y.")
2. Identify the next available ADR number by listing `docs/adrs/`. ADRs are numbered `NNNN` (zero-padded, 4 digits) starting at `0001`.
3. Choose a slug — lowercase, hyphenated, descriptive. File path: `docs/adrs/NNNN-<slug>.md`.

### Phase 2: Context
4. Write the **Context** section: what's true today that makes this decision necessary now? Constraints, forcing functions, prior art.
5. Ask for feedback on the framing — is this the right decision being made at the right layer?

### Phase 3: Options
6. List 2–3 options with their trade-offs. For each: how it works, what it costs, what it buys.
7. If only one option is real, say so explicitly — "rejected alternatives" is its own legitimate section.

### Phase 4: Decision
8. State the chosen option in one paragraph. Explain why over the rejected ones.

### Phase 5: Consequences
9. List positive consequences (what gets better).
10. List negative consequences (what gets worse, or what new problems arise).
11. List follow-ups (work this decision creates: migrations, doc updates, deprecations).

### Phase 6: Status & Save
12. Set status: `Proposed` (under review), `Accepted` (decision made), `Superseded by NNNN` (replaced).
13. Save to `docs/adrs/NNNN-<slug>.md` using the template below.
14. Show the ADR. Ask for feedback.
15. Once approved, commit as `docs(adr): NNNN-<slug>` and push.

## ADR template

```markdown
# NNNN. <Title>

- **Status:** Proposed | Accepted | Superseded by NNNN
- **Date:** YYYY-MM-DD
- **Deciders:** <names or roles>

## Context

<What problem are we solving? What constraints apply? What prior art exists?>

## Options Considered

### Option A: <name>
- How it works:
- Pros:
- Cons:

### Option B: <name>
- How it works:
- Pros:
- Cons:

## Decision

<Chosen option, in one paragraph. Why this over the rejected alternatives.>

## Consequences

### Positive
-

### Negative
-

### Follow-ups
- [ ] <work this creates>
```

## Key Rules

- ADRs are append-only. Never edit a past ADR's *Decision* once accepted — supersede it instead.
- One ADR per decision. Don't bundle two decisions into one record.
- Reference the ADR number from spec/plan/code comments when a non-obvious decision shapes them.
