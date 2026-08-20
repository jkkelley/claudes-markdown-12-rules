# CLAUDE.md — 12-rule template

These rules apply to every task in this project unless explicitly overridden.
Bias: caution over speed on non-trivial work.

## Rule 1 — Think Before Coding
State assumptions explicitly. Ask rather than guess.
Push back when a simpler approach exists. Stop when confused.

## Rule 2 — Simplicity First
Minimum code that solves the problem. Nothing speculative.
No abstractions for single-use code.

## Rule 3 — Surgical Changes
Touch only what you must. Don't improve adjacent code.
Match existing style. Don't refactor what isn't broken.

## Rule 4 — Goal-Driven Execution
Define success criteria. Loop until verified.
Strong success criteria let Claude loop independently.

## Rule 5 — Use the model only for judgment calls
Use for: classification, drafting, summarization, extraction.
Do NOT use for: routing, retries, deterministic transforms.
If code can answer, code answers.

## Rule 6 — Token budgets are not advisory
Per-task: 4,000 tokens. Per-session: 30,000 tokens.
If approaching budget, summarize and start fresh.
Surface the breach. Do not silently overrun.

## Rule 7 — Surface conflicts, don't average them
If two patterns contradict, pick one (more recent / more tested).
Explain why. Flag the other for cleanup.

## Rule 8 — Read before you write
Before adding code, read exports, immediate callers, shared utilities.
If unsure why existing code is structured a certain way, ask.

## Rule 9 — Tests verify intent, not just behavior
Tests must encode WHY behavior matters, not just WHAT it does.
A test that can't fail when business logic changes is wrong.

## Rule 10 — Checkpoint after every significant step
Summarize what was done, what's verified, what's left.
Don't continue from a state you can't describe back.

## Rule 11 — Match the codebase's conventions, even if you disagree
Conformance > taste inside the codebase.
If you think a convention is harmful, surface it. Don't fork silently.

## Rule 12 — Fail loud
"Completed" is wrong if anything was skipped silently.
"Tests pass" is wrong if any were skipped.
Default to surfacing uncertainty, not hiding it.

## Post-merge cleanup

When the user says a PR is merged, perform this cleanup automatically without being asked.
Do not wait for the user to spell out the steps each time.

1. `git fetch origin --prune`
2. `git checkout main`
3. `git merge --ff-only origin/main` to fast-forward local `main` to the merged state.
4. Delete the feature branch locally: `git branch -d <feature-branch>`.
5. Delete the feature branch on the remote: `git push origin --delete <feature-branch>`.
6. Verify the result: on `main`, in sync with `origin/main`, and the feature branch gone from both local and remote.
7. Remove any temporary or scratch directories and scaffolding created during the work.

## The close-out flow

**`CONTEXT_STATE.md` -> PR -> cleanup -> hydration prompt.** In that order, every time.

1. Update `CONTEXT_STATE.md` **on the work branch, as part of the work**, before the PR is opened. It is part of the deliverable, not paperwork that follows it.
2. Open the PR. It carries the code and the state file together, so one review sees the change and the record of the change.
3. After the merge, run `bash .claude/skills/work-order/scripts/work-order.sh close --id WO-...`. It opens its own PR for the merge SHA, the archive move and the `INDEX.md` regeneration. That one is generated bookkeeping and is expected.
4. Leave the hydration prompt at the bottom of `CONTEXT_STATE.md` naming the next work order by **both its id and its full title**, so the next session starts from a file rather than from memory.

Zenith agent instructions

These are common instructions for Zenith's agents across all scenarios.

General Guidelines

- Never use the em dash "—". Use plain dash "-" instead
- When writing commit messages, NEVER auto-add your agent name as co-author
- Never manually modify CHANGELOG.md files or any files that are marked as auto-generated
- When writing or substantially editing long Markdown files, put each full sentence on its own line.
  Preserve normal Markdown structure, but avoid wrapping multiple sentences onto one physical line.
- When making technical decisions, do not give much weight to development cost.
  Instead, prefer quality, simplicity, robustness, scalability, and long term maintainability.
- When doing bug fixes, always start with reproducing the bug in an E2E setting as closely aligned with how an end use
  This makes sure you find the real problem so your fix will actually solve it.
- When end-to-end testing a product, be picky about the UI you see and be obsessed with pixel perfection.
  If something clearly looks off, even if it is not directly related to what you are doing, try to get it fixed along
- Apply that same high standard to engineering excellence: lint, test failures, and test flakiness.
  If you see one, even if it is not caused by what you are working on right now, still get it fixed.

Zenith Opinions

When you are working on something that would benefit from being informed by Zenith's viewpoints, read ~/OPINIONS.md to understand
Testing, use [dotfiles/claude/skills/container-sandbox](https://github.com/jkkelley/dotfiles/tree/main/claude/skills/container-sandbox), if the way your testing doesn't exist, carve out a section and explain what your testing and how you're testing, why you're testing, and how it can help in the future to save time.



