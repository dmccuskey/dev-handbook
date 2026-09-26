# Agent Instructions

These apply to every project, alongside the project's own instructions (its `AGENTS.md`, `CLAUDE.md`, or `docs/development.md`). Where they disagree, the project's instructions win.

This file is an index. Read a guideline when the task calls for it, not up front. Paths are relative to this file.

## Always

- Work on a branch named for the change (`feat/`, `fix/`, `docs/`), created from an up-to-date `main`, never directly on `main`. See [guidelines/git-workflow.md](guidelines/git-workflow.md).
- Leave changes uncommitted for me to review. Commit, merge, and push only when asked; pull requests only sparingly.
- Keep the state of the work in the workspace's `PLAN.md`, never only in the conversation, so the session can be cleared at any time. See [guidelines/agent-workflow.md](guidelines/agent-workflow.md).
- Keep identifying details (real addresses, hostnames, account names, personal paths) out of anything committed to a public repository.

## When the Task Calls for It

| Task | Read |
|---|---|
| Writing, restructuring, or reviewing documentation: README, `docs/`, ADRs | [guidelines/documentation.md](guidelines/documentation.md) |
| Branching, committing, merging, or tracking planned work | [guidelines/git-workflow.md](guidelines/git-workflow.md) |
| Starting or ending a session, planning work, or a large cleanup | [guidelines/agent-workflow.md](guidelines/agent-workflow.md) |
| Starting a README, a documentation home, or an ADR | [templates/](templates/) |
