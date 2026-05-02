---
description: Core design principles governing bridle
---

# Design Principles

## Core Philosophy
- **Manage complexity ruthlessly.** Every decision should reduce cognitive load. Optimize for the reader.
- **Make change easy, then make the easy change.** Localize modifications, minimize risk.
- **Pull complexity downward.** Simple interfaces over simple implementations.
- **When in doubt, choose the boring solution.**

## Construction
- Names reveal intent; use the harness vocabulary (guidance, guardrails, flywheel, workflows, discipline) consistently.
- Slash commands do one thing each. If a command's instructions span multiple unrelated phases, split it.
- Markdown is the implementation. Treat it as code: small files, clear headings, no dead sections.
- **YAGNI** — don't add fields to manifests "in case we need them later". Add when the need is real.
- **KISS** — the simplest command markdown that gets Claude to do the right thing wins.

## Bridle-specific patterns

- The **value lives in the templates and command markdown**, not in any wrapper code (there is no wrapper code).
- Every project-specific value in templates is a `[PLACEHOLDER]` token (uppercase letters, digits, underscores in square brackets). `/generate-harness` finds and replaces them.
- Reference plugin files with `${CLAUDE_PLUGIN_ROOT}` so paths work after the plugin is cached.
- Commands receive arguments via `$ARGUMENTS` (single-string) or `$1`, `$2`, ... (positional). Pick the simpler form.
- Prefer **describing intent** in command markdown over enumerating exact tool calls. Trust Claude to choose tools.

## Anti-Patterns
- Premature abstraction in command instructions ("this could be configurable")
- Speculative placeholders that no command references
- Restating the global `CLAUDE.md` philosophy inside every command — link instead
- Long enumerations of edge cases — describe the goal, let the agent reason
