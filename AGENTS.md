# Agent Instructions

These apply to every project, alongside the project's own instructions (its `AGENTS.md`, `CLAUDE.md`, or `docs/development.md`). Where they disagree, the project's instructions win.

This file is an index. Read a guideline when the task calls for it, not up front. Paths are relative to this file.

## Always

- Work on a branch named for the change (`feat/`, `fix/`, `docs/`), created from an up-to-date `main`, never directly on `main`. See [guidelines/git-workflow.md](guidelines/git-workflow.md).
- Leave changes uncommitted for me to review. Commit, merge, and push only when asked; pull requests only sparingly.
- Keep the state of the work in the workspace's `PLAN.md` (the index: "Now", "Then", every task by link; read its top first) and its task files in `agent/tasks/`, never only in the conversation, so the session can be cleared at any time. See [guidelines/agent-workflow.md](guidelines/agent-workflow.md).
- Keep identifying details (real addresses, hostnames, account names, personal paths) out of anything committed to a public repository.

## When the Task Calls for It

| Task | Read |
|---|---|
| Writing, restructuring, or reviewing documentation: README, `docs/`, ADRs | [guidelines/documentation.md](guidelines/documentation.md) |
| Branching, committing, merging, or tracking planned work | [guidelines/git-workflow.md](guidelines/git-workflow.md) |
| Starting or ending a session, moving a task to its next phase, planning work, or a large cleanup | [guidelines/agent-workflow.md](guidelines/agent-workflow.md) |
| Setting up a new workspace: its `CLAUDE.md`, `PLAN.md` and `agent/` folder | [guidelines/agent-workflow.md](guidelines/agent-workflow.md#setting-up-a-workspace), [templates/](templates/) |
| Updating a workspace to the current layout (e.g. one with a `NEXT.md`) | [CHANGES.md](CHANGES.md) |
| Starting a README, a documentation home, an ADR, a workspace's `CLAUDE.md` and `PLAN.md`, a task file, or a sprint note | [templates/](templates/) |
