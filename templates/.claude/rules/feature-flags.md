# Feature Flags

Feature flags decouple deploy from release. They are also a long-term liability — a flag that outlives its purpose is debt with a switch on it.

## IRON LAW

**EVERY FLAG HAS A LIFECYCLE END-DATE OR REMOVAL TICKET AT BIRTH.**

A flag added without a plan to remove it is a permanent branch in the codebase. At creation, every flag must declare: type (release / experiment / kill-switch / ops / permission), owner, expected expiry condition, and the ticket that will remove it. No exceptions for "we'll remove it after the rollout" — that ticket is filed *before* the flag merges.

## GOLDEN RULES

- **Aim to keep flag count small.** Each flag doubles the configuration matrix on the path it touches.
- **Aim for one decision per flag.** A flag that toggles three things is three flags pretending to be one.
- **Aim to remove flags promptly.** A release flag past its expiry is no longer a flag — it's a configuration option you're paying to maintain.

## Flag types

| Type | Purpose | Lifetime | Removal trigger |
|---|---|---|---|
| **Release** | Ship code dark, enable when ready | Days to weeks | Fully ramped + soak period |
| **Experiment** | A/B test a hypothesis | Weeks | Experiment concludes; winner promoted |
| **Kill-switch** | Disable a risky path under load | Permanent (ops control) | Never — keep |
| **Ops** | Tunable parameter (rate limits, timeouts) | Permanent | Never — keep |
| **Permission** | User-facing entitlement | Permanent | Never — keep |

The first two are *temporary* and **must** be removed. The last three are *permanent* configuration; they are flags only structurally.

## Lifecycle

1. **Born:** flag created with default *off*, expiry condition recorded, removal ticket filed in {{ TASK_TRACKER }}.
2. **Ramping:** progressively enable in environments / cohorts. Monitor.
3. **Ramped:** flag is on for everyone. The "off" path is now dead code.
4. **Removed:** dead-code path deleted, flag removed from configuration, removal ticket closed.

A flag stuck at "ramped" for more than its expiry window is the most common failure mode. Treat that backlog as bug work, not chore work.

## Naming

- Action-oriented: `enable_<feature>`, `use_<new_thing>`.
- Avoid double-negatives: `disable_old_path` confuses every reader.
- Include a stable identifier: `enable_checkout_v2_2026q2`.

## Guardrails

- Default off. Opt-in to enable.
- Reading a flag is a function call, not a global lookup at module-load time.
- Tests cover *both* sides of every conditional flag, until the flag is removed.

## Project-Specific Notes

{{ FEATURE_FLAG_NOTES }}
