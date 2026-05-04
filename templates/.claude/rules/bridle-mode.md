---
description: Bridle execution mode — sync asks every phase, async iterates and stops on ambiguity, autopilot runs end-to-end and reviews at the end
---

# Bridle Mode

**Active mode: sync**

The active mode is set by `/bridle:mode <sync|async|autopilot>`. The three modes change how the agent paces work, nothing else.

## Sync mode (default)

Ask for feedback at each phase boundary in skills and at confirmation prompts in commands. The user drives the pace.

## Async mode

- Do **not** ask for feedback at routine phase boundaries.
- Iterate until the work is done.
- **Stop and report findings** — do not guess — when:
  - Requirements are ambiguous.
  - Rules conflict.
  - A test failure is inscrutable.
  - A choice has non-trivial consequences and no clear winner.

## Autopilot mode

Maximally autonomous. The user engages at kickoff and at the final review.

- Do **not** ask for feedback at routine phase boundaries.
- Do **not** stop on ambiguity — make the best-effort decision and continue. Note the assumption inline so the final-review reader can audit it.
- Iterate end-to-end.
- Surface every assumption, fallback, and judgment call in a structured **assumption log** at the end of the run, alongside the diff and any verification results.

## What all modes preserve

- **Plan-first behavior** — planning still happens; only the user-facing "ask" is skipped in async and autopilot.
- **Scaffolding safety contract** — never silently overwrite a user's file. All modes ask before overwriting; that is a safety rule, not a pace-setting checkpoint.
- **User ownership of commits and pushes** — every mode iterates on *work*; the user still owns the *publish*.
