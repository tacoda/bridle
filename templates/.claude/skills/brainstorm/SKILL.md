---
name: brainstorm
description: Turn a rough idea into an approved spec — Socratic dialogue, 2–3 approaches, written design doc saved to docs/specs/
argument-hint: "[brief description of the idea]"
---

Turn an idea into a spec for **{{ PROJECT_NAME }}**: $ARGUMENTS

If `$ARGUMENTS` is empty, ask the user what they want to brainstorm before starting.

This skill honors `.claude/rules/bridle-mode.md`. Even in autopilot, this skill **always presents the spec for user approval** — implementing without an approved spec is what this skill exists to prevent.

## IRON LAW

**NO IMPLEMENTATION BEFORE AN APPROVED SPEC.**

Do not invoke any implementation skill, write any production code, scaffold any project, or create a feature branch until the user has approved the written spec. Applies to every project regardless of perceived simplicity. "Simple" projects are where unexamined assumptions cause the most wasted work — the spec can be three sentences, but you must present it.

## GOLDEN RULES

- **Aim for one question per message.** If a topic needs more, break it into separate turns.
- **Aim for the user's actual problem, not the user's first sentence.** Restate intent in your own words to surface mismatch early.
- **Aim for a spec a stranger could implement.** If you would have to be in the room to interpret it, it isn't done.
- **Aim for explicit out-of-scope statements.** Naming what is *not* included is half the value of a spec.

## Workflow

### Phase 1: Explore
1. Read `CLAUDE.md`, `HARNESS.md`, `GLOSSARY.md`, and any relevant `.claude/features/*.md` for context.
2. Skim recent commits in the affected area.
3. If the request describes multiple independent subsystems, flag it. Help the user decompose into sub-projects, then brainstorm the first one. Each sub-project gets its own spec → plan → implementation.

### Phase 2: Clarify
4. Ask clarifying questions **one at a time**. Prefer multiple-choice when possible.
5. Focus on: purpose, constraints, success criteria, who the user is, what changes if this exists.
6. Stop when you can write a spec a stranger could implement.

### Phase 3: Approaches
7. Propose 2–3 approaches with explicit trade-offs.
8. State your recommendation with reasoning.
9. Ask the user to pick one (or pick a fourth path).

### Phase 4: Spec sections
10. Present the spec in sections, scaled to complexity. Get approval after each section before continuing. Sections to include:
    - **Goal** — one sentence.
    - **Out of scope** — what this is *not*.
    - **User-visible behavior** — what changes from the user's perspective.
    - **Success criteria** — how we know it's done.
    - **Open questions** — things still undecided, with proposed defaults.

### Phase 5: Write the spec
11. Save the spec to `docs/specs/YYYY-MM-DD-<short-topic>.md`.
12. Self-review: scan for placeholders, contradictions, ambiguity, scope creep. Fix inline.
13. Ask the user to read the file and confirm.

### Phase 6: Hand off
14. Once approved, suggest invoking `implement-change` (or `fix-bug` if the spec is for a bug fix). Pass the spec path. Do not invoke the implementation skill from inside this one.

## Key rules

- One question per message. Never combine "ask a clarifying question" with "propose an approach" in the same message.
- The spec is approved by the user, not by you.
- The spec lives at `docs/specs/YYYY-MM-DD-<topic>.md` so the next session can find it.
