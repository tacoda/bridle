# bridle

Agent project harness manager for Claude Code. A plugin that scaffolds and maintains the system that lets an AI coding agent produce correct, high-quality code consistently.

Bridle is the plugin successor to [sellier](https://github.com/tacoda/sellier), the Python CLI predecessor. Same templates, simpler install.

> **Status:** pre-1.0. Marketplace publication pending; commands stabilizing.

## Background

- [Announcing sellier](https://dev.to/tacoda/announcing-sellier-4489)
- [Building a harness — standardizing agentic coding in a real codebase](https://dev.to/tacoda/building-a-harness-how-we-standardized-agentic-coding-in-a-real-codebase-4oab)

## Install

```
/plugin marketplace add tacoda/tacoda-marketplace
/plugin install bridle@tacoda-marketplace
```

## Quick start

Open a project in Claude Code:

```
/bridle:generate-harness
```

Scaffolds `CLAUDE.md` and `.claude/` (rules, agents, skills, commands), then walks the codebase to fill placeholders. Existing files are never silently overwritten — conflicts are shown and confirmed individually.

## Commands

| Pillar | Commands |
|---|---|
| **harness** | `/generate-harness` |
| **guidance** | `/add-rule`  `/scope-rule`  `/show-rules`  `/orient` |
| **guardrails** | `/check`  `/verify`  `/smell`  `/audit` |
| **flywheel** | `/learn`  `/retro`  `/stale-rules` |
| **workflows** | `/new-skill`  `/new-agent`  `/new-command`  `/run-skill`  `/spawn-reviewers`  `/ci-status` |
| **discipline** | `/debt-map`  `/touch-clean`  `/harness-health` |

All commands invoke as `/bridle:<command>`.

## Safety

Every command that writes files into your project follows a strict contract:

- Never silently overwrites.
- Conflicts are shown as diffs; default action is **skip**.
- `CLAUDE.md` is propose-additions when it already exists — your content is never edited without explicit approval.
- `.claude/settings.json` merges keys additively.

## License

MIT
