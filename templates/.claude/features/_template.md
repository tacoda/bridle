<!-- Feature doc template. Copy to `<feature-name>.md` and fill in. Keep under ~60 lines. -->

---
description: <one sentence: what this feature is and which user-facing capability it implements>
---

# <Feature Name>

## Summary

<One short paragraph: what this feature does, why it exists, and the trigger that invokes it.>

## Key Files

<List the files an agent needs to read to make a correct change. Group by layer if helpful.>

- `<path>` — <role>
- `<path>` — <role>

## Domain Terms

<Terms specific to this feature. Link to `GLOSSARY.md` rather than redefining a term used elsewhere.>

- **<term>** — <meaning>

## Entities / Data

<Schemas, tables, or core types touched by this feature. Column-by-column only when it matters; otherwise a link.>

## Endpoints / Entry Points

<HTTP routes, CLI commands, queue handlers, or service methods that invoke this feature.>

| Method | Path / Name | Purpose |
|---|---|---|

## Gotchas

<Anything an agent will get wrong on first attempt — invariants, ordering, hidden coupling.>
