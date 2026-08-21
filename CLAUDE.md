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

**`CONTEXT_STATE.md` -> hydration prompt -> one PR -> merge -> cleanup -> the command.** In that order, every time.

**There is exactly one pull request per ticket.** A second PR carrying only state files doubles the review surface for one piece of work and leaves `main` briefly describing a world that no longer exists.

1. Update `CONTEXT_STATE.md` **on the work branch, as part of the work**. New checkpoint at the **top**, newest first. It is part of the deliverable, not paperwork that follows it.
2. Generate the successor's hydration prompt **on the same branch** with `.claude/skills/hydration-prompt/scripts/hydration.sh`. Never hand-edit `HYDRATION.md`; the script owns its ordering and its window.
3. Leave the interview-ready retro as a note on the ticket, then `work-order.sh done`, still on the same branch.
4. Push once and open **one** PR. It carries the code, the state file and the hydration prompt together, so one review sees the change and the record of the change.
5. After the merge, run `bash .claude/skills/work-order/scripts/work-order.sh close --id WO-...`. It backfills the merge SHA, archives the ticket and regenerates `INDEX.md` **straight to `main`** - no second PR. It falls back to a PR only if that push is rejected.
6. Hand back **both** the hydration prompt and the command that starts the next session, then hold. Do not start it.

**Never open a PR whose only content is `CONTEXT_STATE.md` or `HYDRATION.md`.**

**Check whether your PR has already merged before every commit once it is open.** A squash merge rewrites the SHA and closes the branch, so a commit pushed seconds later is stranded on a branch nothing points at. Recovery is fast-forward `main`, branch fresh, cherry-pick, delete both sides - but noticing late is avoidable, and it is the usual reason the one-PR rule gets broken.

## Starting a session

`HYDRATION.md` is the prompt that starts the next session, and the nine before it.

**Read the top entry only.** It is current and complete on its own; everything below it has been superseded and is kept for history, not for reading. This is the one sliding-window file where retention depth and reading depth deliberately differ: ten full hydration prompts is roughly fifteen thousand words of superseded instructions.

Newest on top. Adding an entry removes the tenth in the same commit. Entries are never numbered, never renumbered and never edited in place; a correction is a new entry.

The session is launched with the command the `hydration-prompt` skill hands back, which is emitted rather than typed so it cannot disagree with the entry it points at:

```sh
claude -p "Read Hydration Prompt located at $FULL_PATH_TO_FILE, Process work order $WO_ID per its acceptance criteria after you've read it." \
  --permission-mode bypassPermissions \
  -n "Session: $WO_ID - $WO_TITLE"
```

When the work is not a ticket, the acceptance-criteria clause is dropped rather than left pointing at nothing, and the session takes its name from the entry's title:

```sh
claude -p "Read Hydration Prompt located at $FULL_PATH_TO_FILE" \
  -n "Session: $TITLE"
```

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



