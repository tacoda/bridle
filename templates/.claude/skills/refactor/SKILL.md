---
name: refactor
description: Execute a behavior-preserving refactor — small steps from the catalog, tests after each step, no behavior change in the same commit
argument-hint: "[what to refactor — file, function, or pattern]"
---

Refactor in **{{ PROJECT_NAME }}**: $ARGUMENTS

If `$ARGUMENTS` is empty, ask what to refactor and what the desired end state is.

This skill honors `.claude/rules/bridle-mode.md`. In **paired**, confirm after each catalog step. In **solo**, iterate small steps and stop on test failures or unanticipated coupling. In **autopilot**, run the catalog steps end-to-end and surface the diff and test runs at final review.

## IRON LAWS

**NO BEHAVIOR CHANGE IN A REFACTOR COMMIT.**

A refactor preserves observable behavior. The test suite that passed before each step must pass after. If the refactor reveals a bug, that bug is fixed in a *separate* commit — never folded into the refactor.

**NO REFACTOR WITHOUT A GREEN BASELINE.**

Run `{{ TEST_COMMAND }}` before the first step. If anything is failing, the refactor is not the right work — fix the failure first, then refactor.

## GOLDEN RULES

- **Aim for catalog refactorings.** Named, small, behavior-preserving moves: extract method, inline variable, replace conditional with polymorphism, introduce parameter object. Compose from the catalog rather than improvising.
- **Aim to run tests after every step.** Steps small enough to be safe; tests fast enough to be cheap. If your steps run for a minute each, the steps are too big.
- **Aim to commit after each meaningful step.** A 1-step refactor is one commit. A 12-step refactor is twelve commits or one squashed-but-traceable history. Either way, each step is reviewable.

## Catalog (most-used)

- **Extract Method / Function** — move a coherent block into a named callable.
- **Inline Variable** — collapse a temporary that adds no clarity.
- **Rename** — names that no longer reveal intent.
- **Move Method / Field** — relocate to the class that owns the data it touches.
- **Replace Conditional with Polymorphism** — switch/case over a type tag → method dispatch.
- **Replace Magic Number with Constant** — name the value at its meaning.
- **Introduce Parameter Object** — group correlated parameters into a value type.
- **Extract Class / Module** — split a class doing two things into one each.
- **Replace Inheritance with Composition** — when an `is-a` was forced into a `has-a` problem.
- **Decompose Conditional** — extract the predicate, the then-branch, and the else-branch into named methods.

## Workflow

### Phase 1: Baseline
1. Run `{{ TEST_COMMAND }}`. Must be green.
2. State the refactor goal in one sentence: "Move this responsibility to <X>", "Replace this conditional with <Y>", "Extract <Z> from <W>".
3. Show the starting code. Ask for feedback on the goal before touching anything.

### Phase 2: Plan the catalog steps
4. Decompose the goal into a sequence of catalog refactorings.
5. For each step: name the move, the file/function involved, and what stays observably the same.
6. Show the plan. Ask for feedback.

### Phase 3: Execute one step at a time
For each step:
7. Make the change.
8. Run the relevant tests. If green, proceed. If red, revert the step and reconsider.
9. Commit as `refactor(<scope>): <step description>` (or accumulate and squash later, if project policy prefers).

### Phase 4: Review
10. Show the cumulative diff. Spawn `refactor-changes` and `self-review` in parallel.
11. Apply review findings.
12. Run `/pre-commit`.
13. Push.

### Phase 5: Done
14. The behavior is the same; the structure is better. Smell that went away → note it; smell that remains → consider whether a follow-up refactor is warranted.

## Anti-Patterns

- **Refactor + feature in one commit.** The reviewer cannot tell which lines moved and which lines changed behavior.
- **Refactor without tests.** The "tests" of a refactor are the tests it didn't break. No tests → no refactor — write characterization tests first.
- **Big-bang rewrites.** A refactor that touches 200 files in one PR is not a refactor; it is a bet.
- **Refactoring code you're about to delete.** If the next PR removes the file, leave it alone.
