# Changelog

All notable changes to bridle are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.2.1] — 2026-05-06

### Fixed
- `/bridle:patch-harness` now substitutes `{{ PLACEHOLDER }}` tokens in **new** files added by an upgrade. Previously, new files were written verbatim, leaving raw tokens in the consumer's harness. Phase 2 marks placeholder-bearing new files in its proposal list, reuses any value already bound elsewhere in the consumer's harness as the default, falls back to project evidence for unbound tokens, and substitutes once before write. Existing consumer files are still never re-substituted.

## [1.2.0] — 2026-05-06

### Added
- **IRON LAW / GOLDEN RULES rule conventions.** Two rhetorical markers used across rules and skills. **IRON LAW** is non-negotiable — violating the letter is violating the spirit. **GOLDEN RULES** are ideals to aim for; deviation requires reasoning. Documented in `templates/CLAUDE.md` and the README.
- `templates/.claude/rules/verification-before-completion.md` (always loaded) — IRON LAW: no completion claims without fresh verification evidence in the current turn. Defines a five-step gate (identify → run → read → verify → claim) and a claim-to-evidence mapping table.
- `templates/.claude/rules/tdd-anti-patterns.md` (path-scoped to test paths) — catalog of nine TDD anti-patterns with diagnoses and fixes (writing the test after the code, mocking internal collaborators, snapshot tests as specs, over-mocked tests that pass when production is broken, etc.).
- `brainstorm` skill — Socratic spec extraction. Asks clarifying questions one at a time, proposes 2–3 approaches with trade-offs, presents the spec in sections for incremental approval, and writes an approved spec to `docs/specs/YYYY-MM-DD-<topic>.md`. IRON LAW: no implementation skill, code, scaffold, or branch until the user has approved a written spec.
- `subagent-tasks` skill — execute a multi-task plan by dispatching a fresh subagent per task with two-stage review (spec compliance, then code quality). For plans with 3+ mostly-independent tasks. IRON LAWS: fresh subagent per task, no inherited context, two reviews before the next task starts, verify the diff yourself.
- `worktree` skill — set up an isolated workspace for parallel work. Detects existing isolation, creates a worktree with consent, runs setup, confirms a green baseline before reporting ready. IRON LAW: no worktree creation without explicit consent. Soft ceiling at three concurrent worktrees.
- `finish-branch` skill — verify tests pass, then present merge / PR / keep / discard options and execute the user's choice. Worktree-aware cleanup. IRON LAW: no merge or PR without green verification in the current turn.
- `/bridle:patch-harness` — bring forward template additions after a bridle plugin upgrade. Adds new files, diffs placeholder-free files, advisory-only on placeholder-bearing files (never re-runs placeholder substitution), additive JSON-merge for `settings.json`. The upgrade-time counterpart to `/bridle:generate-harness`.
- Durable spec and plan artifacts. `brainstorm` writes specs to `docs/specs/YYYY-MM-DD-<topic>.md`; `implement-change` writes plans to `docs/plans/YYYY-MM-DD-<topic>.md` with checkbox-style tasks. The next session can pick up where this one left off.

### Changed
- `fix-bug` skill — adds an IRON LAW (no fixes without root cause) and GOLDEN RULES. Phase 2 now requires multi-component diagnostic instrumentation at each component boundary before guessing which layer is wrong. Phase 3 traces the bad value back to its source so the fix lands at the source, not the symptom. Phase 8 suggests `finish-branch`.
- `implement-change` skill — adds IRON LAWS (no production code without a failing test first; no completion claims without fresh verification) and GOLDEN RULES. Phase 0 reads an approved spec from `docs/specs/` or invokes `brainstorm` to produce one. Phase 1 writes the implementation plan to `docs/plans/` and offers to hand off to `subagent-tasks` for plans with 3+ independent tasks. Phase 6 suggests `finish-branch`.
- `templates/.claude/rules/commits.md` — adds IRON LAWS (no co-authors / no AI attribution; no commits with failing pre-commit checks) and GOLDEN RULES (one logical change per commit; structural changes separated from behavioral; messages explain *why*).
- `templates/.claude/rules/tests.md` — cross-links to `tdd-anti-patterns.md`.
- `templates/CLAUDE.md` — rules table includes the two new rules; new "Skills" table lists all seven scaffolded skills; "On-demand context" section adds `docs/specs/` and `docs/plans/`; new section documents the IRON LAW / GOLDEN RULES convention.
- README — daily-workflow loop expands with `brainstorm`, `subagent-tasks`, `worktree`, and `finish-branch` entry points; scaffolded-skills table updated; commands table includes `/patch-harness`; new section explains the IRON LAW and GOLDEN RULES vocabulary.
- `/bridle:generate-harness` template-tree listing updated to include the new rules and skills.

## [1.1.0] — 2026-05-04

### Added
- `HARNESS.md` — canonical definition of the five harness pillars (guidance, guardrails, workflows, flywheel, discipline), with a Mermaid diagram and a section per pillar. Auto-loaded into context via `@HARNESS.md` import in `CLAUDE.md`. Scaffolded into consumer projects.
- `onboard` skill — walks a new engineer through orient, environment verification, picking a starter task, and shipping a first PR via `/implement-change`.
- `/bridle:fan-out` — dispatch one agent in parallel over N independent items (the dual of `/bridle:spawn-reviewers`). Includes guardrails for sequential work like TDD loops.
- **Execution modes** — three modes change how the agent paces work along a spectrum of agent autonomy: `paired` (default; pair programming, ask at every phase), `solo` (iterate independently; stop on ambiguity), `autopilot` (run end-to-end; log assumptions; review at the end). Set with `/bridle:mode`. Lives as a rule at `.claude/rules/bridle-mode.md`, version-controlled with the consumer's project. No mode bypasses the scaffolding safety contract or auto-publishes commits.
- Plugin vs. template separation invariant — codified in `.claude/rules/design-principles.md` and enforced by the `self-review` agent. Forbids the same artifact basename from appearing in both the plugin root and `templates/.claude/`.
- "Concepts", "Daily workflow", "Mode", and expanded "Quick start" sections in the README to act as a project walkthrough.
- "You own your harness" articulation in the README — the harness lives in the consumer's repo and runs without bridle as a runtime dependency.
- "Scaffolded skills" table in the README — lists `implement-change`, `fix-bug`, and `onboard` with descriptions.
- `keywords` in `plugin.json` for marketplace discoverability.

### Changed
- Placeholder syntax migrated from `[NAME]` to `{{ NAME }}` (mustache style). Visually distinct from markdown links; reduces false-positive risk in detection. The `/generate-harness` regex tolerates optional spacing.
- Install instructions now recommend **project-level install** (marketplace add once, pin in `.claude/settings.json`, commit) so every contributor opens the repo with the same harness tooling. User-level install kept as an alternative.
- Harness vocabulary unified on **pillars** (previously drifted between "components" and "pillars" across files).
- `harness-health` renamed its "Component coverage" section to "Workflow coverage" to align with pillar terminology.

### Fixed
- `/bridle:generate-harness` template-tree listing was missing `fix-bug`; now lists all three skills (`implement-change`, `fix-bug`, `onboard`).

## [1.0.0] — 2026-05-02

Initial release.

### Added
- Plugin manifest (`.claude-plugin/plugin.json`) and self-hosted marketplace distribution.
- 21 `/bridle:*` slash commands organized across the five harness pillars.
- Template tree in `templates/` (`CLAUDE.md`, `.claude/rules/`, `.claude/agents/`, `.claude/commands/`, `.claude/skills/`) scaffolded by `/bridle:generate-harness`.
- `implement-change` and `fix-bug` skills.
- Five review agents: `review-functional`, `review-security`, `refactor-changes`, `ci-diagnose`, `self-review`.
- Four base rules: `design-principles`, `tests`, `security`, `commits`.
- Scaffolding safety contract (never silently overwrite; `CLAUDE.md` propose-additions; `settings.json` additive merge).
