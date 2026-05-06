---
name: postmortem
description: Write a blameless postmortem after an incident — timeline, impact, RCA (5-whys / causal chain), what went well/poorly, action items — saved to docs/postmortems/YYYY-MM-DD-<slug>.md
argument-hint: "[incident title or short slug]"
---

Document an incident postmortem for **{{ PROJECT_NAME }}**: $ARGUMENTS

If `$ARGUMENTS` is empty, ask for the incident title and rough timeframe.

This skill honors `.claude/rules/bridle-mode.md`. Postmortems are largely interview-driven — in any mode, ask the user for the facts before drafting. Don't autopilot interview answers.

## IRON LAWS

**BLAMELESS LANGUAGE ONLY.**

Postmortems describe systems and decisions, never people. "Alice deployed the bad change" is wrong. "A change was deployed without a canary because the deploy tool didn't enforce one" is right. The goal is to fix the system that allowed the incident, not to assign blame to the human who tripped over it. Names appear only as roles in the response timeline ("the on-call engineer rolled back at 14:32"), never as causes.

**NO POSTMORTEM WITHOUT A ROOT CAUSE ANALYSIS SECTION.**

A timeline + an impact statement is an incident report, not a postmortem. The RCA — the explicit causal walk from symptom to underlying system property — is the artifact's reason to exist. If the RCA is missing, weak, or substitutes "human error" for actual analysis, the postmortem is incomplete.

## GOLDEN RULES

- **Aim for a tight timeline before any analysis.** Reconstruct facts first; interpret them second. Times in UTC, sources cited.
- **Aim past "human error" in RCA.** "An engineer made a mistake" is a symptom. The root cause is the system property that depended on no engineer ever making that mistake.
- **Aim for action items that change the *system*.** "Be more careful" is not an action item. "Add a deploy-tool check that rejects deploys without a canary in environment X" is.

## Workflow

### Phase 1: Frame
1. Confirm the title, date, and rough duration. Slug the title (lowercase, hyphenated).
2. Filename: `docs/postmortems/YYYY-MM-DD-<slug>.md`.

### Phase 2: Timeline
3. Reconstruct the incident in chronological order. UTC times. Each entry has a *what happened* and a *source* (log line, alert, chat message, commit).
4. Distinguish *cause events* (the thing that actually broke something) from *response events* (what the team did about it).
5. Ask the user to fill any gaps you can't reconstruct.

### Phase 3: Impact
6. Quantify impact: users affected, requests dropped, data loss, revenue, duration. Numbers, not adjectives.
7. Note what the system *should* have done to limit impact and didn't.

### Phase 4: Root Cause Analysis (RCA)
8. Pick a technique appropriate to the incident:
   - **5 Whys** — repeatedly ask "why did that happen" against each answer until you reach a system property, not a person. Stop when "why" no longer makes sense.
   - **Causal chain** — write the linear sequence: trigger → propagation → detection failure → impact → recovery. Each link names the system property that allowed it.
   - **Fishbone (Ishikawa)** — for incidents with multiple contributing categories (people, process, tooling, data, environment). One spine per category.
9. Distinguish three things explicitly:
   - **Trigger:** what set this off (a deploy, a traffic spike, a config change). Triggers are events; root causes are not.
   - **Root cause:** the latent system property that turned the trigger into an incident. Always a system property.
   - **Contributing factors:** anything that made the incident worse, longer, or harder to detect.
10. Write the root cause as a single sentence about the system. Not "X did Y," but "the system allowed Y because Z."
11. Show the RCA. Ask whether the user agrees with where you stopped the "why" chain — stopping too early misses the real cause; stopping too late blames physics.

### Phase 5: What went well, what went poorly, where we got lucky
12. **What went well** — detection mechanisms that fired, runbooks that worked, mitigations that contained blast radius.
13. **What went poorly** — gaps in detection, slow response, unclear ownership, missing rollback path.
14. **Where we got lucky** — outcomes that depended on chance and could go the other way next time. Often the most important section.

### Phase 6: Action items
15. List action items. Each has: what changes, owner, due date, ticket link.
16. Bias toward items that change the *system*: alerts, automation, guardrails, documentation. Avoid "be more careful" / "add training."
17. Mark each item **High / Medium / Low** priority and follow up to ground items in {{ TASK_TRACKER }} tickets.
18. Each action item should reference which RCA finding or contributing factor it addresses — orphan action items are noise.

### Phase 7: Save & circulate
19. Save to `docs/postmortems/YYYY-MM-DD-<slug>.md`.
20. Show the postmortem. Ask for feedback.
21. Once approved, commit as `docs(postmortem): YYYY-MM-DD-<slug>` and push.
22. Suggest follow-ups: open the action-item tickets, schedule a review meeting, link from the {{ TASK_TRACKER }} incident if one exists.

## Postmortem template

```markdown
# Postmortem — <Title>

- **Date:** YYYY-MM-DD
- **Duration:** <hh:mm>
- **Severity:** <SEV1 | SEV2 | SEV3>
- **Author:** <name>
- **Status:** Draft | Reviewed | Final

## Summary

<Two-sentence non-technical summary.>

## Impact

<Users affected, requests, data, duration. Numbers.>

## Timeline (UTC)

| Time | Event | Source |
|------|-------|--------|

## Root Cause Analysis

### Trigger
<What set this off.>

### Root Cause
<Single sentence about the system.>

### Causal Chain (or 5 Whys)
1. <Why did the impact happen?> Because …
2. <Why did that happen?> Because …
3. …

### Contributing Factors
- <factor> — <why it mattered>

## What Went Well

-

## What Went Poorly

-

## Where We Got Lucky

-

## Action Items

| Priority | Action | Addresses | Owner | Due | Ticket |
|----------|--------|-----------|-------|-----|--------|
```

## Key Rules

- Postmortems are append-only once finalized. New learnings → new postmortem.
- A postmortem with no action items is suspicious — the incident taught nothing?
- Action items without owners or dates are wishes, not commitments.
