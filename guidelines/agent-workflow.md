# Agent Workflow

How I work with coding agents (Claude Code or any other harness). It sits alongside the [git workflow](git-workflow.md) and [documentation guidelines](documentation.md).

## The Main Idea

**A session can be cleared at any time without losing anything.** The state of the work lives in files and GitHub issues, never only in the conversation. Long conversations cost more with every turn, so sessions stay short: one task, then a checkpoint, then `/clear`.

## The Plan File

Each workspace has one plan file, `PLAN.md`, at its root: the repository root, or the folder that groups several repositories. It is the first thing a new session reads, and it holds only what is needed to continue:

```markdown
# Plan

## Now
The task in progress: what it is, where it stands (branch, PR), the next step.

## Next
Queued tasks, most important first, one or two lines each, linking to GitHub issues where they exist.

## Waiting
Things blocked on someone or something, and on what.
```

- **Decided work** for one repository goes in that repository's GitHub issues; the plan file links to them instead of repeating them.
- **Undecided ideas** go under "Possible Future Changes" in the repository's `docs/development.md` (see [Planned Work and Ideas](documentation.md#planned-work-and-ideas)).
- **Finished work leaves the plan.** Its record is the commits, pull requests, closed issues and changelogs, not the plan file.
- **Lasting knowledge** about the workspace, such as its layout, tools, build steps and gotchas, goes in the agent instructions (`CLAUDE.md` or `AGENTS.md`), not the plan.

## Normal Flow

1. **Start:** read the agent instructions and `PLAN.md`. Take the task under "Now", or the top of "Next".
2. **Work:** on a branch, per the [git workflow](git-workflow.md). Test.
3. **Update the docs** the change affects: the repository's docs and changelog, the agent instructions for anything learned about the workspace, and new issues or plan entries for anything found but not done.
4. **Checkpoint:** commit, and merge or open a pull request. Rewrite "Now" to the next task. Nothing should exist only in the conversation.
5. **`/clear`** and start the next session at step 1.

One task per session. If a task turns out bigger than expected, stop at a checkpoint and split the rest into plan entries or issues.

## Emergency Mode

Sometimes a project is in bad shape: years out of date, broken builds, many problems that depend on each other. Fixing one thing at a time with a cleared session in between would mean rediscovering the same context over and over, so one longer session is fine:

- **Declare it:** say that this session is a cleanup, and start the plan file right away.
- **Keep writing things down as you go.** The plan file and agent instructions grow during the session, so the session could still be cleared if it had to be.
- **Still one branch per change,** tested, merged in dependency order.
- **Exit** once every remaining problem can be described in a plan entry or an issue and done in a session of its own. Then checkpoint, trim the plan file to "Now / Next / Waiting", and `/clear`. From then on, normal flow.
