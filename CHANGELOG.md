# Changelog

All notable changes to bridle are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

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
