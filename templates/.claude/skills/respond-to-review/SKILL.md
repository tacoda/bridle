---
name: respond-to-review
description: Walk through open PR review comments, propose fixes, apply with confirmation, mark threads resolved — closes the review loop
argument-hint: "[PR number, optional — defaults to the current branch's PR]"
---

Respond to PR review comments for **{{ PROJECT_NAME }}**: $ARGUMENTS

If `$ARGUMENTS` is empty, find the PR for the current branch via `gh pr view --json number`. If there is no PR for the current branch, stop and report.

This skill honors `.claude/rules/bridle-mode.md`. In **paired**, ask before applying each fix. In **solo**, apply uncontroversial fixes and stop on judgment calls (architectural pushback, scope expansion). In **autopilot**, propose-and-apply each fix, log judgment calls, and present the bundle at final review.

## IRON LAW

**NO COMMENT IS RESOLVED WITHOUT EITHER A CHANGE OR A REPLY.**

A reviewer left a comment to be answered. Silently dismissing it disrespects the review and breaks the trust loop. Either: (a) make the change and reply with the commit SHA, or (b) reply explaining why the change wasn't made and ask for confirmation. "Resolved" with no trace is forbidden.

## GOLDEN RULES

- **Aim for one commit per logical comment cluster, not one commit per comment.** Three comments on the same function fold into one commit.
- **Aim to push back when push-back is right.** "The reviewer is wrong" is a valid finding — but it must be argued, not ignored.
- **Aim to update tests when changing behavior in response to review.** A review comment that changes behavior without updating tests is a review comment that didn't happen.

## Workflow

### Phase 1: Inventory
1. Fetch open review comments:
   ```
   gh api repos/<owner>/<repo>/pulls/<n>/comments
   ```
   Filter to threads not yet resolved.
2. List each comment with: author, file:line, comment body, your initial classification — `apply`, `discuss`, `reject with reasoning`.
3. Group comments by file or by logical concern.
4. Show the inventory. Ask the user which to walk through first.

### Phase 2: Walk
For each comment:
5. Restate the comment's intent in one sentence to confirm you understood it.
6. Propose a response:
   - **apply:** show the proposed code change.
   - **discuss:** propose a reply with a question or counter-proposal.
   - **reject:** propose a reply explaining why the suggestion doesn't apply, and confirm with the user before posting.
7. Ask for feedback before applying.
8. Apply the change. Run the relevant test if behavior changed.

### Phase 3: Commit & push
9. Group applied changes into commits. Conventional commit prefix that matches the change type (`fix`, `refactor`, `test`, `docs`).
10. Run `/pre-commit`. Push.

### Phase 4: Reply on each thread
11. For each addressed comment, post a reply: either the commit SHA that resolved it, or the reasoning if you didn't change the code.
12. Mark threads as resolved after the user confirms they're satisfied — not before.

### Phase 5: Re-request review
13. If the reviewer asked for re-review, request it via `gh pr review`.
14. Summarize the round in one short comment: what changed, what was discussed, what was kept as-is.

## Key Rules

- Never resolve a thread the reviewer opened without their input. They opened it; they close it.
- "LGTM with comments" still counts as comments — walk them.
- Don't bundle "respond to review" with unrelated work. The diff should answer the review and nothing else.
