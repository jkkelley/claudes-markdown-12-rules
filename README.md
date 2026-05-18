# Claude's Markdown 12 Rules

This repository contains the shared Claude configuration files used across projects — the 12-rule operating contract and the ignore list that keeps Claude's context window clean.

---

## CLAUDE.md — The 12-Rule Operating Contract

`CLAUDE.md` is read by Claude Code at the start of every session. It sets the behavioral contract Claude must follow for all tasks in a project. Copying or symlinking this file into any repo ensures consistent, disciplined AI-assisted development.

### The Rules

**Rule 1 — Think Before Coding**
State assumptions explicitly. Ask rather than guess. Push back when a simpler approach exists. Stop when confused.

**Rule 2 — Simplicity First**
Minimum code that solves the problem. Nothing speculative. No abstractions for single-use code.

**Rule 3 — Surgical Changes**
Touch only what you must. Don't improve adjacent code. Match existing style. Don't refactor what isn't broken.

**Rule 4 — Goal-Driven Execution**
Define success criteria. Loop until verified. Strong success criteria let Claude loop independently.

**Rule 5 — Use the model only for judgment calls**
Use for: classification, drafting, summarization, extraction. Do NOT use for: routing, retries, deterministic transforms. If code can answer, code answers.

**Rule 6 — Token budgets are not advisory**
Per-task: 4,000 tokens. Per-session: 30,000 tokens. If approaching budget, summarize and start fresh. Surface the breach. Do not silently overrun.

**Rule 7 — Surface conflicts, don't average them**
If two patterns contradict, pick one (more recent / more tested). Explain why. Flag the other for cleanup.

**Rule 8 — Read before you write**
Before adding code, read exports, immediate callers, shared utilities. If unsure why existing code is structured a certain way, ask.

**Rule 9 — Tests verify intent, not just behavior**
Tests must encode WHY behavior matters, not just WHAT it does. A test that can't fail when business logic changes is wrong.

**Rule 10 — Checkpoint after every significant step**
Summarize what was done, what's verified, what's left. Don't continue from a state you can't describe back.

**Rule 11 — Match the codebase's conventions, even if you disagree**
Conformance > taste inside the codebase. If you think a convention is harmful, surface it. Don't fork silently.

**Rule 12 — Fail loud**
"Completed" is wrong if anything was skipped silently. "Tests pass" is wrong if any were skipped. Default to surfacing uncertainty, not hiding it.

---

## .claudeignore — Keeping Context Clean

`.claudeignore` works like `.gitignore` but for Claude Code's context window. Files and directories listed here are excluded from the files Claude reads when exploring a codebase. Without it, Claude wastes context tokens on `node_modules`, lock files, and build artifacts — none of which contain useful signal for reasoning about code.

### What's excluded and why

| Pattern | Reason |
|---|---|
| `**/node_modules/` | Dependency trees — never edited directly, thousands of files |
| `**/package-lock.json`, `**/go.sum`, `**/poetry.lock`, `**/Cargo.lock` | Lock files are machine-generated and change constantly; no useful context |
| `prospector-fe/dist/`, `prospector-fe/.next/`, `**/build/`, `**/target/` | Compiled output — redundant with source |
| `prospector-events/bin/` | Compiled Go binaries |
| `**/__pycache__/`, `**/*.pyc` | Python bytecode — redundant with source |
| `**/.worktrees/` | Stale git worktree artifacts |
| `**/.superpowers/`, `**/.claude/agents/` | IDE and agent runtime cache — not source |
| `**/node_modules/.cache` | Build tool cache inside dependencies |
