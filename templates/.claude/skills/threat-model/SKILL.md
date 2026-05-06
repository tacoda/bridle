---
name: threat-model
description: Build a threat model for a feature before implementation — assets, entry points, threats (STRIDE), mitigations — saved to docs/threats/<feature>.md
argument-hint: "[feature name or spec path]"
---

Threat-model a feature in **{{ PROJECT_NAME }}**: $ARGUMENTS

If `$ARGUMENTS` is empty, ask which feature or spec to model. Prefer modeling at the *spec* stage, before implementation — that is where the design decisions still cost nothing to change.

This skill honors `.claude/rules/bridle-mode.md`. In **paired**, ask after each phase. In **solo**, complete the model and stop only if the trust boundary is unclear. In **autopilot**, present the full model at final review and log assumptions.

## IRON LAW

**EVERY ENTRY POINT MUST HAVE EACH STRIDE CATEGORY EVALUATED.**

A model with "no threats" is a model that hasn't looked. Walk every entry point against Spoofing, Tampering, Repudiation, Information disclosure, Denial of service, Elevation of privilege. "Not applicable" is a valid finding — but only after considering it.

## GOLDEN RULES

- **Aim to model before implementation.** A threat model after the code is written is a security review with extra steps. The point is to shape design.
- **Aim to identify trust boundaries explicitly.** Where does data cross from untrusted to trusted? Where does authority change? Those are the lines threats cross.
- **Aim for mitigations that are testable.** "Validate input" is not a mitigation. "Reject `Content-Length` > 1MB and emit 413" is.

## Workflow

### Phase 1: Read the spec
1. Read the spec at `docs/specs/...` (or the feature description if no spec exists).
2. Summarize the feature in two sentences.
3. List the assets it touches (data, capabilities, secrets, infrastructure).

### Phase 2: Map the system
4. Sketch the data flow: actors → entry points → components → data stores → downstream services.
5. Mark trust boundaries on the sketch — where authority or trust level changes.
6. Show the sketch and ask for feedback on the boundaries.

### Phase 3: Enumerate threats (STRIDE)
7. For each entry point, walk STRIDE:
   - **S**poofing — can an attacker pretend to be someone they aren't?
   - **T**ampering — can data be modified in transit or at rest by someone unauthorized?
   - **R**epudiation — can an actor deny they performed an action?
   - **I**nformation disclosure — can data leak to an unauthorized observer?
   - **D**enial of service — can the system be made unavailable?
   - **E**levation of privilege — can an actor gain capabilities they shouldn't have?
8. For each threat, record: severity (high/med/low), likelihood (high/med/low), mitigation.

### Phase 4: Identify mitigations
9. List mitigations as concrete, testable controls. Authentication, rate limits, input validation, output encoding, audit logs, monitoring.
10. Map each mitigation to one or more enumerated threats.
11. Identify which mitigations are *missing* and need to be added to the spec / implementation plan.

### Phase 5: Save & review
12. Save to `docs/threats/<feature>.md` using the template.
13. Show the model. Ask for feedback.
14. Suggest follow-ups: spec updates, plan additions, test cases for the mitigations.

## Threat-model template

```markdown
# Threat Model — <Feature>

- **Date:** YYYY-MM-DD
- **Spec:** `docs/specs/...`
- **Reviewers:** <names>

## Summary

<Two-sentence feature description.>

## Assets

- <data, capability, secret, etc.>

## Data Flow

<sketch or text description of actors → entry points → components → data stores>

## Trust Boundaries

- <boundary>: <what crosses it>

## Threats

| ID | Entry point | STRIDE | Description | Severity | Likelihood | Mitigation |
|----|-------------|--------|-------------|----------|------------|------------|

## Mitigations to add

- [ ] <missing control to wire into the spec/plan>
```

## Key Rules

- A threat model is a living doc — updated when the feature changes shape, not when convenient.
- Findings flow into the spec or plan. A threat model that doesn't change anything downstream is paper.
- Pair with the `review-security` agent after implementation: model shapes design, review verifies execution.
