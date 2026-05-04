# Feature Documentation

On-demand context for agents working in a specific feature or domain.

These docs are **not** auto-loaded. The agent reads one when exploring the relevant area before making a change. Each doc captures the irreducible context — entities, endpoints, gotchas — that an agent needs and would otherwise reinvent.

## When to add a doc

- A feature has been touched twice and the agent reinvented the same context both times.
- A domain has non-obvious invariants that aren't captured by code or by a rule.
- New contributors (human or AI) need a head start on the area.

## Conventions

- Keep each doc under ~60 lines. If it's growing past that, the feature probably needs to be split.
- Cite real files and real entry points. Speculation is worse than nothing.
- Domain terms link to `GLOSSARY.md`; don't redefine.
- Use `_template.md` as the starting shape, or run `/bridle:add-feature-doc <name>`.
