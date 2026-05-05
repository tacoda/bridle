# bridle

Agent project harness manager for Claude Code. A plugin that scaffolds and maintains the system that lets an AI coding agent produce correct, high-quality code consistently.

Bridle is the plugin successor to [sellier](https://github.com/tacoda/sellier), the Python CLI predecessor. Same templates, simpler install.

## Demo

<video src="https://github.com/user-attachments/assets/ef5bc9e4-2b33-481f-be96-9a1a3db39626" autoplay muted loop playsinline controls></video>

## Background

- [Announcing sellier](https://dev.to/tacoda/announcing-sellier-4489)
- [Building a harness — standardizing agentic coding in a real codebase](https://dev.to/tacoda/building-a-harness-how-we-standardized-agentic-coding-in-a-real-codebase-4oab)

## Concepts

A **harness** is the system that lets an AI agent produce correct, high-quality code consistently. Bridle's harness has five pillars:

- **Guidance** — `CLAUDE.md`, rules in `.claude/rules/`, on-demand feature docs in `.claude/features/`, and `GLOSSARY.md` for ubiquitous language. Together they shape what the agent writes before it writes a single line.
- **Guardrails** — lint, tests, type-checks the agent runs and won't bypass.
- **Workflows** — agents, commands, and skills as runnable procedures.
- **Flywheel** — review feedback updates rules; rules shape the next conversation.
- **Discipline** — practices that keep the others fresh — debt maps, boy-scout rule, harness health.

The canonical reference is [`HARNESS.md`](HARNESS.md), which is also scaffolded into your project and auto-loaded into context via `@HARNESS.md` from `CLAUDE.md`.

## Install

Recommended: install bridle at the **project level** so every contributor opens the repo with the same harness tooling.

1. Add the marketplace (once per machine):

   ```
   /plugin marketplace add tacoda/tacoda-marketplace
   ```

2. Pin bridle in your project's `.claude/settings.json` and commit it:

   ```json
   {
     "enabledPlugins": {
       "bridle@tacoda-marketplace": true
     }
   }
   ```

Claude Code installs bridle the next time the project opens.

To install for yourself only (no commit), run `/plugin install bridle@tacoda-marketplace` instead.

## Quick start

Open a project in Claude Code:

```
/bridle:generate-harness
```

Scaffolds `CLAUDE.md`, `HARNESS.md` (the canonical definition of the harness pillars), and `.claude/` (rules, agents, skills, commands), then walks the codebase to fill placeholders. Existing files are never silently overwritten — conflicts are shown and confirmed individually.

After the scaffold, you have:

- `CLAUDE.md` — project context loaded on every conversation.
- `HARNESS.md` — pillar definitions.
- `GLOSSARY.md` — ubiquitous domain vocabulary with aliases-to-avoid.
- `.claude/rules/` — behavioral rules (some always-loaded, some path-scoped).
- `.claude/features/` — on-demand domain context, read when exploring a specific feature.
- `.claude/agents/` — review and analysis agents.
- `.claude/commands/` — single-step utilities (e.g., `/pre-commit`).
- `.claude/skills/` — multi-phase workflows (`/implement-change`, `/fix-bug`, `/onboard`).
- `.claude/settings.json` — Claude Code permissions and settings.

## Daily workflow

A typical day-to-day loop with bridle in your project:

1. **Pick up a task.** Invoke the right skill: `/implement-change <description>` for features, `/fix-bug <description>` for bugs, `/onboard` for new contributors.
2. **The skill runs phases** — TDD, review, commit. In **paired** mode (default), it pauses for your feedback at each phase. In **solo**, it iterates autonomously and stops on ambiguity. In **autopilot**, it runs end-to-end and you review at the end. (See [Mode](#mode).)
3. **Before commit, `/pre-commit`** runs lint / tests / type-check. The agent fixes failures rather than bypassing them.
4. **When a reviewer flags a pattern, run `/bridle:learn`** (or edit the relevant rule directly). The next conversation already knows.

Less frequent maintenance:

- `/bridle:harness-health` — single-page report on rule freshness and consistency.
- `/bridle:stale-rules` — flag rules unchanged for too long.
- `/bridle:retro` — mine recent commits for patterns worth turning into rules.
- `/bridle:audit` — walk the codebase against every rule.

## Mode

Bridle has three execution modes that change how the agent paces work, nothing else. The names sit on a spectrum of agent autonomy:

| Mode | Asks during work? | Stops on ambiguity? |
|---|---|---|
| `paired` (default) | yes — every phase boundary | n/a (always asks) |
| `solo` | no | yes — stop and report |
| `autopilot` | no | no — best-effort, log assumptions, review at end |

- **paired** — pair programming. You and the agent work together; it pauses for your feedback at each phase.
- **solo** — the agent works independently but raises a hand on hard problems (ambiguous requirements, conflicting rules, inscrutable test failures).
- **autopilot** — fully autonomous. The agent runs end-to-end and surfaces every assumption in a structured log at the final review.

```
/bridle:mode               # print current mode
/bridle:mode paired        # pair at every phase (default)
/bridle:mode solo          # iterate; stop on ambiguity
/bridle:mode autopilot     # run end-to-end; review the diff and assumption log at the end
```

Mode lives in `.claude/rules/bridle-mode.md` — version-controlled with your project, so a team can settle on a default. **No mode** bypasses the scaffolding safety contract or auto-publishes commits — those stay user-driven across all three.

`/onboard` always runs in paired mode regardless of the active mode, because the engineer drives the pace; that's the point.

## You own your harness

The files bridle scaffolds live in your repo, not inside the plugin. Once they're in place, the harness runs on Claude Code alone — bridle's commands just make maintaining it easier.

- **Version-controlled.** `CLAUDE.md` and `.claude/` ship with your code. Diff it, branch it, review it like any other source.
- **Works without bridle.** Uninstall the plugin and the harness keeps working — Claude Code reads `.claude/` natively. Bridle is the tool that maintains the harness, not a runtime dependency of it.
- **No hidden copies.** Bridle's commands operate on the files in your repo, not on duplicates cached inside the plugin. The plugin is the engine; your `.claude/` is the configuration.
- **Editable by hand.** Open the markdown, change a rule, commit. The flywheel commands pick up your edits on the next run.

## Commands

All commands invoke as `/bridle:<command>`.

| Pillar | Command | Description |
|---|---|---|
| harness | `/generate-harness` | Scaffold the harness into the current project and substitute placeholders |
| harness | `/mode` | Read or set the bridle execution mode (paired, solo, autopilot) |
| guidance | `/add-rule` | Create a new rule file in `.claude/rules/` |
| guidance | `/add-feature-doc` | Scaffold a new on-demand feature doc in `.claude/features/` |
| guidance | `/scope-rule` | Adjust an existing rule's `paths:` scope |
| guidance | `/show-rules` | List every rule with description, scope, and last-modified date |
| guidance | `/orient` | Walk the codebase and propose context additions to `CLAUDE.md` |
| guardrails | `/check` | Run lint, type-check, and tests scoped to the current diff |
| guardrails | `/verify` | Run `/check` plus parallel functional and security review agents |
| guardrails | `/smell` | Spot refactoring opportunities in the current diff |
| guardrails | `/audit` | Walk the codebase against every rule and report violations |
| flywheel | `/learn` | Convert review feedback into a rule update |
| flywheel | `/retro` | Mine recent commits for patterns that should become rules |
| flywheel | `/stale-rules` | Flag rules that haven't been updated and propose actions |
| workflows | `/new-skill` | Scaffold a new project-local skill |
| workflows | `/new-agent` | Scaffold a new project-local agent — review (read-only) or scoped worker (write-capable, fenced to a path tree) |
| workflows | `/new-command` | Scaffold a new project-local slash command |
| workflows | `/run-skill` | Manually invoke a project-local skill by name |
| workflows | `/spawn-reviewers` | Fan out every review agent on the current diff |
| workflows | `/fan-out` | Fan out one agent in parallel over N independent items |
| workflows | `/ci-status` | Show the latest CI runs for the current branch |
| discipline | `/debt-map` | Heatmap of where rule debt is concentrated |
| discipline | `/touch-clean` | Boy-scout rule: surface smells in files in the current diff |
| discipline | `/harness-health` | Dashboard of rule freshness, coverage, and flywheel cadence |

## Scaffolded skills

`/bridle:generate-harness` writes these multi-phase skills into `.claude/skills/`. They're invoked as `/<name>` from your project, run on Claude Code alone, and are yours to edit.

| Skill | Description |
|---|---|
| `/implement-change` | Implement a change (feature, follow-up) using TDD — requirements through to a PR |
| `/fix-bug` | Diagnose and fix a bug using TDD — replicate, wrap in a failing test, fix, ship |
| `/onboard` | Walk a new engineer through orient, environment verification, and a first starter PR |

## Safety

Every command that writes files into your project follows a strict contract:

- Never silently overwrites.
- Conflicts are shown as diffs; default action is **skip**.
- `CLAUDE.md` is propose-additions when it already exists — your content is never edited without explicit approval.
- `.claude/settings.json` merges keys additively.

## License

MIT
