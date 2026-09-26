# Agent Workflow

How I work with coding agents (Claude Code or any other harness). It sits alongside the [git workflow](git-workflow.md) and [documentation guidelines](documentation.md).

## The Main Idea

**A session can be cleared at any time without losing anything.** The state of the work lives in files, never only in the conversation. Long conversations cost more with every turn, so sessions are usually one task, then a checkpoint, then `/clear`. That's a default, not a rule: because clearing is always safe, whether to clear now is a judgment call (see [When to Clear](#when-to-clear)).

## The Plan File

Each workspace has one plan file, `PLAN.md`, at its root: the repository root, or the folder that groups several repositories. It is the first thing a new session reads, and it holds only what is needed to continue:

```markdown
# Plan

## Now
The task in progress: what it is, where it stands (branch, uncommitted or committed), the next step.

## Next
Queued tasks, most important first, one or two lines each, linking to GitHub issues where they exist.

## Waiting
Things blocked on someone or something, and on what.
```

- **Decided work** goes in the plan file while the project is under active work. As it winds down, the remaining future work moves into detailed GitHub issues (see [Planned Work](git-workflow.md#planned-work)); the plan links to an issue instead of repeating it.
- **Undecided ideas** go under "Possible Future Changes" in the repository's `docs/development.md` (see [Planned Work and Ideas](documentation.md#planned-work-and-ideas)).
- **Finished work leaves the plan.** Its record is the commits and changelogs, not the plan file.
- **Lasting knowledge** about the workspace, such as its layout, tools, build steps and gotchas, goes in the agent instructions (`CLAUDE.md` or `AGENTS.md`), not the plan.

## Normal Flow

1. **Start:** read the agent instructions and `PLAN.md`. Take the task under "Now", or the top of "Next".
2. **Work:** on a branch, per the [git workflow](git-workflow.md). Test. [Save as you go](#save-as-you-go).
3. **Update the docs** the change affects: the repository's docs and changelog, the agent instructions for anything learned about the workspace, and plan entries for anything found but not done.
4. **Review:** the agent leaves the change uncommitted and lists what changed, repository by repository; I review it in the editor.
5. **Checkpoint:** once I approve, commit, merge, and push (see the [git workflow](git-workflow.md)). Rewrite "Now" to the next task. Check the conversation for anything not yet saved. Nothing should exist only in the conversation.
6. **Say so:** the agent tells me that everything is saved and `/clear` is safe, and reminds me how to start the next session: "let's continue with PLAN.md".
7. **`/clear`** and start the next session at step 1.

### When to Clear

Keeping the docs ready for a restart is not optional; clearing is. Weigh the cost of a longer conversation against the cost of rebuilding context after a `/clear`:

- **Keep going** when the task grew but the rest is a few more rounds on the same topic, or when the next step needs what is already in the conversation (a test run just read, a diff just discussed).
- **Clear** when switching to a different task, when the conversation has grown long (many rounds, large file reads or command output), or when the next step would start mostly fresh anyway.
- **Split** a task into plan entries only when the rest is a separate piece of work, not just because it took longer than expected.

The agent may suggest a `/clear` at a natural checkpoint, with the reason, but it's my call.

## Save As You Go

Write things down when they come up, not at the end, so a checkpoint only has to confirm nothing is left:

| What | Where |
|---|---|
| An important technical decision about how the software works, with its reasons and the options rejected | an ADR in the repository's `docs/decisions/` (see [Decision Records](documentation.md#decision-records-docsdecisions)), or a plan entry to write one. Smaller choices need no record beyond the commit message; when unsure, the agent asks me |
| A preference about how I like things done | the relevant dev-handbook guideline |
| A gotcha about the workspace: tools, builds, quirks, commands that work | the agent instructions (`CLAUDE.md` or `AGENTS.md`) |
| A bug or improvement found but not fixed | a plan entry (a GitHub issue if others should see it, or the project is winding down), or "Possible Future Changes" if undecided |
| A useful script written along the way | the workspace (e.g. a `tools/` or `scripts/` folder), noted in the agent instructions |

Anything written to a temporary scratch folder is lost with the session: move what's worth keeping.

## Emergency Mode

Sometimes a project is in bad shape: years out of date, broken builds, many problems that depend on each other. Fixing one thing at a time with a cleared session in between would mean rediscovering the same context over and over, so one longer session is fine:

- **Declare it:** say that this session is a cleanup, and start the plan file right away.
- **Keep writing things down as you go** ([save as you go](#save-as-you-go) applies even more). The plan file and agent instructions grow during the session, so the session could still be cleared if it had to be.
- **Still one branch per change,** tested, merged in dependency order.
- **Exit** once every remaining problem can be described in a plan entry and done in a session of its own. Then checkpoint, trim the plan file to "Now / Next / Waiting", and `/clear`. From then on, normal flow.
