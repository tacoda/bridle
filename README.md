# bridle

Agent project harness manager for Claude Code. A plugin that scaffolds and maintains the system that lets an AI coding agent produce correct, high-quality code consistently.

Bridle is the plugin successor to [sellier](https://github.com/tacoda/sellier), the Python CLI predecessor. Same templates, simpler install.

## Background

- [Announcing sellier](https://dev.to/tacoda/announcing-sellier-4489)
- [Building a harness — standardizing agentic coding in a real codebase](https://dev.to/tacoda/building-a-harness-how-we-standardized-agentic-coding-in-a-real-codebase-4oab)

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

Scaffolds `CLAUDE.md`, `HARNESS.md` (the component definition of the harness), and `.claude/` (rules, agents, skills, commands), then walks the codebase to fill placeholders. Existing files are never silently overwritten — conflicts are shown and confirmed individually.

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
| guidance | `/add-rule` | Create a new rule file in `.claude/rules/` |
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
| workflows | `/new-agent` | Scaffold a new project-local agent |
| workflows | `/new-command` | Scaffold a new project-local slash command |
| workflows | `/run-skill` | Manually invoke a project-local skill by name |
| workflows | `/spawn-reviewers` | Fan out every review agent on the current diff |
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
