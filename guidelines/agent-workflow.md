# Agent Workflow

How I work with coding agents (Claude Code or any other harness). It sits alongside the [git workflow](git-workflow.md) and [documentation guidelines](documentation.md).

## The Main Idea

**A session can be cleared at any time without losing anything.** The state of the work lives in files, never only in the conversation. Long conversations cost more with every turn, so sessions are usually one task, then a checkpoint, then `/clear`. That's a default, not a rule: because clearing is always safe, whether to clear now is a judgment call (see [When to Clear](#when-to-clear)).

## The Plan Files

Each workspace keeps the state of the work in two files at its root (the repository root, or the folder that groups several repositories), split by how often they are read:

- **`NEXT.md`** is the first thing a new session reads, so it is short: the task in progress, with its status, and the next few tasks.
- **`PLAN.md`** is the backlog: the goal of the current round of work, everything else that is decided, and what is waiting. It is read at the checkpoint, when a task moves up into `NEXT.md`, and when planning, not at the start of every session.

```markdown
# Next

## Now
The task in progress and its status block: the phase, where it stands
(repository, branch, uncommitted or committed), what is done and what is
left, decisions, the next step (see "The Status Block" below).

## Then
The next few tasks (about five), most important first, a few lines each,
enough to start one without reading PLAN.md.
```

```markdown
# Plan

The goal of the current round of work.

## Later
Queued tasks after the ones in NEXT.md, most important first, one or two
lines each, linking to GitHub issues where they exist.

## Waiting
Things blocked on someone or something, and on what.
```

- **Each task is in one place.** A task moves from "Later" in `PLAN.md` to "Then" in `NEXT.md` when there's room, and leaves `PLAN.md`. At the checkpoint, the finished task leaves `NEXT.md`, the top of "Then" becomes "Now", and "Then" is refilled from `PLAN.md`.
- **Decided work** goes in the plan files while the project is under active work. As it winds down, the remaining future work moves into detailed GitHub issues (see [Planned Work](git-workflow.md#planned-work)); the plan links to an issue instead of repeating it.
- **Undecided ideas** go under "Possible Future Changes" in the repository's `docs/development.md` (see [Planned Work and Ideas](documentation.md#planned-work-and-ideas)).
- **Finished work leaves the plan.** Its record is the commits and changelogs, not the plan files.
- **Only state, kept short.** Every session reads `NEXT.md`, so every line there costs tokens each time; `PLAN.md` is read less often but should stay short too. Procedures that repeat for each item (a checklist per repository), long bug lists, and research detail go in linked notes (e.g. `notes/`), with a line saying when to read them.
- **Lasting knowledge** about the workspace, such as its layout, tools, build steps and gotchas, goes in the agent instructions (`CLAUDE.md` or `AGENTS.md`), not the plan.
- **A workspace without `NEXT.md`** (set up before it existed) keeps "Now" and "Next" in `PLAN.md`, and a session starts there. To split it, move "Now" and the top few of "Next" into a new `NEXT.md` from the template.

## Setting Up a Workspace

A workspace is the folder where agent sessions start: usually a folder that holds the repository (or several related ones), or, for a small project, the repository itself. This handbook needs no setup per workspace: every harness loads it from its global instructions (see the [README](../README.md#setup)). A workspace needs only its own three files, at its root. In Claude Code, [`/init-workspace`](../skills/init-workspace/SKILL.md) looks at the folder, proposes a setup along the steps below, and sets it up once approved.

| File | Holds | Template |
|---|---|---|
| `CLAUDE.md` (or `AGENTS.md`) | lasting knowledge: what the workspace is, its layout, how to build and test, gotchas, and where it differs from this handbook | [workspace-CLAUDE.md](../templates/workspace-CLAUDE.md) |
| `NEXT.md` | the task in progress and the next few, read at the start of every session | [workspace-NEXT.md](../templates/workspace-NEXT.md) |
| `PLAN.md` | the backlog: the goal, later tasks, what is waiting | [workspace-PLAN.md](../templates/workspace-PLAN.md) |
| `sprints/` | a [sprint note](#sprint-notes) for each task with a Research phase, read on demand; created with the first one | [workspace-sprint.md](../templates/workspace-sprint.md) |

1. **Pick the root.** Prefer a **workspace folder** around the repository, `~/development/<name>/<repo>/`. It is a private place for what doesn't belong in the repository:
   - tools that aren't the project's own: built runtimes, virtual environments, pinned command-line tools (often machine-specific and not relocatable)
   - notes and research: cached documentation, investigation logs
   - scripts written along the way
   - agent instructions and a plan that mention local paths, or that shouldn't be in a public repository's history
   - room for related repositories, or [worktrees](https://git-scm.com/docs/git-worktree) for other branches (`git worktree add ../<repo>-dev dev`: a second checkout that shares one history, instead of a second clone)

   Anything other contributors need, such as build steps, tests, or a script the project depends on, still belongs in the repository and its docs; the workspace's instructions link there instead of repeating it.

   The repository alone is enough for a small project whose tools come from its own package manager and that has no local notes: then the workspace root is the repository root.
2. **Always start sessions there.** Claude Code reads `CLAUDE.md` from the folder a session starts in and its parents, and keeps its memory per starting folder: a session started in a subfolder misses both.
3. **Write the agent instructions** from the template. Keep them short and factual; a workspace's instructions win where they disagree with this handbook, so list the differences (such as a default branch other than `main`) there. Running Claude Code's `/init` gives a first draft to trim.
4. **Write the plan files** from the templates: the first task under "Now" in `NEXT.md`, the goal and the rest in `PLAN.md`.
5. **Put them under version control,** so a bad edit can be undone and a lost disk doesn't lose them:
   - *The repository as the workspace:* commit them with the code. If the repository is public, keep identifying details out of them (see [Public Repositories](git-workflow.md#public-repositories)), or list `NEXT.md` and `PLAN.md` in `.gitignore` if it holds notes that shouldn't be published.
   - *A workspace folder:* make the folder itself a small **private** repository that tracks only its own files and ignores the checkouts and installed tools. Its instructions describe the local layout, so it stays **local, with no remote**: it's for undoing a bad edit and reviewing how the files changed, and the machine's backup covers a lost disk:

     ```gitignore
     # track only the workspace's own files, not the checkouts
     /*
     !/.gitignore
     !/CLAUDE.md
     !/NEXT.md
     !/PLAN.md
     !/notes/
     !/scripts/
     !/sprints/
     ```

6. **Start** the first session with "let's continue with NEXT.md", then follow the [normal flow](#normal-flow): Start, Research, Perform, Verify, Finalize.

### Design Documents and Roadmaps

A project often starts with a design worked out elsewhere, such as a chat or a shared document: architecture, decisions, a roadmap of phases. It is not the plan files. The plan says what is being done now; the design says what the project is and where it is going. Each part has its place in the repository (see the [documentation guidelines](documentation.md)):

| Part of the design | Goes to |
|---|---|
| How it works: structure, flow, components | `docs/architecture.md` |
| Each important decision, with its reasons and the options rejected | an ADR in `docs/decisions/` |
| Decided features, by phase | a GitHub milestone per phase, an issue per feature (`### Goal`, `### Why`, details); see [Planned Work](git-workflow.md#planned-work) |
| Ideas not decided, research | "Possible Future Changes" in `docs/development.md` |
| The current phase's next steps | `NEXT.md` and `PLAN.md`, linking the milestone and issues |

While the design is still changing, it can stay where it is: the workspace's `CLAUDE.md` links it and says when to read it (before design work, not every session). Once implementation starts, moving it into the repository is the first task in the plan, so that the code and its design are versioned together and readable by any agent or contributor. The old document then gets a note at the top that it has moved.

## Normal Flow

Every task goes through five phases. Each phase ends with a **handoff**: what the next phase needs is written down, so the session can be cleared there without losing anything.

| Phase | What happens | Handoff |
|---|---|---|
| **Start** | Read the agent instructions and `NEXT.md`. Take the task under "Now", or the top of "Then". Create the branch, per the [git workflow](git-workflow.md). | The status block names the task, the branch and the phase. |
| **Research** | Explore the code, reproduce the problem, try fixes in a scratch copy. | Findings, with their evidence, in the task's [sprint note](#sprint-notes); the chosen approach and its assumptions; bugs found but not fixed as plan entries or Known Issues. |
| **Perform** | Write the code and the docs. Update every doc the change affects: the repository's docs and changelog, the agent instructions for anything learned about the workspace, plan entries for anything found but not done. | Uncommitted changes; Done and Left in the status block. |
| **Verify** | Check the change the way the project checks things: tests, the Quick Start, the examples. The workspace's agent instructions say how. | The results in the sprint note and the status block. |
| **Finalize** | I review the change; the agent checkpoints and says so (below). | Commits, the plan files updated, the next task named. |

In coding work, Perform and Verify alternate in small loops (change, test, change again) until the change works; a clear point between them is rare. A small task runs through the phases in one go, without a sprint note.

**Finalize** in detail:

1. **Review:** the agent leaves the change uncommitted and lists what changed, repository by repository; I review it in the editor.
2. **Checkpoint:** once I approve, commit, merge, and push (see the [git workflow](git-workflow.md)). Update the plan files: the finished task leaves `NEXT.md`, the next one becomes "Now", and "Then" is refilled from `PLAN.md`. Mark the sprint note done. Check the conversation for anything not yet saved. Commit the workspace repository, if the workspace folder is one. Nothing should exist only in the conversation. In Claude Code, [`/allthethings`](../skills/allthethings/SKILL.md) does this step and the next.
3. **Say so:** the agent tells me that everything is saved and `/clear` is safe, names the task the next session would take (the new "Now" in `NEXT.md`) in a line or two, and reminds me how to start it: "let's continue with NEXT.md". Seeing the next task first gives me the chance to reorder the plan before starting it.
4. **`/clear`** and start the next session at Start.

### The Status Block

The task in progress keeps its status under "Now" in `NEXT.md`, updated at the end of each phase, so that "let's continue with NEXT.md" resumes in the right phase:

```markdown
## Now

**<Task name>**: <what it is, in a sentence or two.>

- Phase: <Start, Research, Perform, Verify or Finalize>
- Where: <repository, branch; uncommitted or committed>
- Done: <what is finished so far>
- Left: <what remains>
- Decisions: <choices made along the way that the rest depends on>
- Next step: <the first thing the next session does>
- Sprint note: [sprints/<file>.md](sprints/<file>.md)
```

It stays a summary; the evidence goes in the sprint note.

### When to Clear

Keeping the docs ready for a restart is not optional; clearing is. The phase handoffs are the natural **clear points**:

- **After Research:** the main one. Research reads a lot and keeps little; Perform needs only the findings and the approach.
- **After Perform, or before the review:** optional, when the conversation has grown large.
- **After Finalize:** always; the next task starts fresh.

Between them, weigh the cost of a longer conversation against the cost of rebuilding context after a `/clear`:

- **Keep going** when the task grew but the rest is a few more rounds on the same topic, or when the next step needs what is already in the conversation (a test run just read, a diff just discussed). Small tasks skip the clear points.
- **Clear** when switching to a different task, when the conversation has grown long (many rounds, large file reads or command output), or when the next step would start mostly fresh anyway.
- **Split** a task into plan entries only when the rest is a separate piece of work, not just because it took longer than expected.

Before suggesting a clear, the agent checks that the handoff is complete: the status block is current, the sprint note has what the next phase needs, and nothing exists only in the conversation. It suggests the clear with the reason; it's my call, and I run `/clear` (or `/compact <what to keep>` to keep a summary).

### Sprint Notes

A task with a Research phase gets a **sprint note**: one file in the workspace's `sprints/` folder, named `<yyyy-mm-dd-hhmm>-<task>.md`, started from [the template](../templates/workspace-sprint.md). It holds what doesn't fit in the status block: the evidence behind the approach, and what was tried.

- **One section per phase,** added to at each handoff, never rewritten; the frontmatter (`project`, `phase`, `status`, `branch`, `repos`) says where the task stands.
- **Research records evidence, not only conclusions:** `file:line` locations, the commands that reproduce the problem and their output (trimmed), the approaches considered and why they were rejected, and the **assumptions** the chosen approach rests on.
- **Perform and Verify record failed attempts** and why they failed, so they aren't tried again, and the test results.
- **When a fix fails,** reload the sprint note instead of researching again, and recheck its assumptions first: a failed fix usually means one of them was wrong.
- **Read on demand only,** never at session start; the status block links to it. At the checkpoint its status becomes `done`, and it stays as the record of how the work was done.

Sprint notes are plain Markdown with Markdown links (not wikilinks), so they read in any editor, and the workspace folder can be opened as an [Obsidian](https://obsidian.md) vault.

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

- **Declare it:** say that this session is a cleanup, and start the plan files right away.
- **Keep writing things down as you go** ([save as you go](#save-as-you-go) applies even more). The plan files and agent instructions grow during the session, so the session could still be cleared if it had to be.
- **Still one branch per change,** tested, merged in dependency order.
- **Exit** once every remaining problem can be described in a plan entry and done in a session of its own. Then checkpoint, trim the plan files to their sections (`NEXT.md`: Now, Then; `PLAN.md`: Later, Waiting), and `/clear`. From then on, normal flow.
