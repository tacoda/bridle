---
description: Analyze the codebase and propose high-level and specific context additions to CLAUDE.md
---

Walk the codebase, distill what's there, and propose additions to `CLAUDE.md`.

## Steps

### 1. Survey the codebase

Read whichever exist:
- Top-level directory listing (one level)
- Build/package files: `package.json`, `pyproject.toml`, `composer.json`, `Cargo.toml`, `go.mod`, `Gemfile`
- `README.md`
- `.github/workflows/`, `.gitlab-ci.yml`, `.circleci/`
- `git log --oneline -20` for recent activity signals
- Identify the largest directories under `src/`, `app/`, `lib/`

### 2. Distill into sections

Draft content for each of these CLAUDE.md sections:

- **Project Overview** — one paragraph: what the project is, who uses it, key constraints.
- **Layout** — directory tree with a one-line summary per top-level entry.
- **Key Abstractions** — the 3–5 most-touched modules, base classes, or shared utilities.
- **Where to find things** — lookup table:
  | Concern | Location |
  |---|---|
- **Domain Vocabulary** — terms that appear repeatedly in code or commits, with a one-line gloss.

Where evidence is missing, write `unknown` and ask the user — do not invent.

### 3. Propose additions

If `CLAUDE.md` does not exist → tell the user; suggest running `/bridle:generate-harness` first.

If `CLAUDE.md` exists → present each drafted section as a numbered proposed addition (heading + body). The user accepts/rejects items individually. Apply only what is explicitly accepted; never edit content the user did not approve.

### 4. Show the diff and confirm.

## Rules

- Don't invent facts. If a section can't be evidenced, omit it or mark `unknown`.
- Keep sections short — overview ≤ 5 sentences, layout ≤ 10 lines, vocabulary ≤ 8 entries. CLAUDE.md is reference, not a tutorial.
- Read-only on the codebase. Only `CLAUDE.md` is written, and only with confirmation.
- Follow the contract in the harness's `.claude/rules/scaffolding.md` for any write to `CLAUDE.md`.
