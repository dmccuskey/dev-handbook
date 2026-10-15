# Agent Workflow

How I work with coding agents (Claude Code or any other harness). It sits alongside the [git workflow](git-workflow.md) and [documentation guidelines](documentation.md).

## The Main Idea

**A session can be cleared at any time without losing anything.** The state of the work lives in files, never only in the conversation. Long conversations cost more with every turn, so sessions are usually one task, then a checkpoint, then `/clear`. That's a default, not a rule: because clearing is always safe, whether to clear now is a judgment call (see [When to Clear](#when-to-clear)).

## The Plan Files

Each workspace keeps the state of the work in files, split by how often they are read:

- **`PLAN.md`**, at the workspace root, is the index: the goal of the current round of work and every task, one line each with a link, in the order they'll be done. A session reads its "Now" and "Then" first.
- **A task file** in `agent/tasks/` says what one task is and where it stands. The task in progress is read at the start of every session.
- **A [sprint note](#sprint-notes)** in `agent/sprints/` records what happened in one round of work on a task. It is read only when needed.

Because each task's detail lives in its own file, `PLAN.md` stays short however much is planned.

### PLAN.md

```markdown
# Plan

The goal of the current round of work, a line or two.

## Now
- [<Task in progress>](agent/tasks/<slug>.md)

## Then
- [<Next task>](agent/tasks/<slug>.md)
- …

## <Any structure that fits the project>
A line or two on how this group works, linking a note for its procedure.
- [<Task>](agent/tasks/<slug>.md)

## Waiting
- [<Task>](agent/tasks/<slug>.md): <blocked on whom or what>
```

- **"Now" and "Then" come first:** the task in progress and the next few (about five), most important first. Reordering them is how I change what comes next.
- **Below them, any structure** that fits the project: a cleanup flow, fixes in dependency order, releases (`## Release 1` with `### 1.0`, `### 1.1`), and `## Waiting` for what is blocked. A group heading may carry a line or two of context and link a note for its procedure.
- **One line per task, with a link.** A tiny task (a typo, a one-line fix) can be a plain line until it is taken or needs detail.
- **Finished tasks leave `PLAN.md`.** Their task files stay, marked done, as the record, with the commits and changelogs.

### Task Files

`agent/tasks/<slug>.md`, started from [the template](../templates/workspace-task.md), says **what we're doing**. A task can be any size: one fix, or a move that takes weeks.

```markdown
---
status: active        # queued, active, waiting or done
phase: Research       # Start, Research, Perform, Verify or Finalize
repos: [<repository>]
branch: <branch>
---

# <Task Name>

What it is and why, a few lines; link the GitHub issue if there is one.

## Status
- Done: …
- Left: …
- Decisions: …
- Next step: …
- Sprint: [<file>](../sprints/<file>.md)

## <Any section the task needs: steps, notes, instructions>
```

- **`## Status` is the [status block](#the-status-block)**, the only required section. The frontmatter holds the short fields (they show up as Properties in [Obsidian](https://obsidian.md)); the status block holds the prose.
- **Add sections as needed:** the steps as a checklist, notes, instructions for the task, an optional field like `release` in the frontmatter.
- **A step that needs more than a few lines of its own becomes its own task**, linked from the parent's steps. The parent stays readable as an overview.
- **Only what the task needs to resume.** Evidence (tracebacks, command output, failed attempts) goes in the sprint note, which is read less often.

### What Goes Where

- **Decided work** goes in `PLAN.md` and the task files while the project is under active work. As it winds down, the remaining future work moves into detailed GitHub issues (see [Planned Work](git-workflow.md#planned-work)); the plan links to an issue instead of repeating it.
- **Undecided ideas** go under "Possible Future Changes" in the repository's `docs/development.md` (see [Planned Work and Ideas](documentation.md#planned-work-and-ideas)).
- **Procedures** that repeat for each item (a checklist per repository), long bug lists, and research detail go in linked notes in `agent/notes/`, with a line saying when to read them.
- **Lasting knowledge** about the workspace, such as its layout, tools, build steps and gotchas, goes in the agent instructions (`CLAUDE.md` or `AGENTS.md`), not the plan.
- **An older workspace** may keep the task in progress in a `NEXT.md` next to `PLAN.md`, its backlog in `PLAN.md`, and its notes at the root. It still works: a session starts in `NEXT.md`. To update it, follow [CHANGES.md](../CHANGES.md), or run `/init-workspace` there in Claude Code.

## Setting Up a Workspace

A workspace is the folder where agent sessions start: usually a folder that holds the repository (or several related ones), or, for a small project, the repository itself. This handbook needs no setup per workspace: every harness loads it from its global instructions (see the [README](../README.md#setup)). A workspace needs only its own files: two at its root, the rest in an `agent/` folder, so they stay apart from the checkouts. In Claude Code, [`/init-workspace`](../skills/init-workspace/SKILL.md) looks at the folder, proposes a setup along the steps below, and sets it up once approved.

| File | Holds | Template |
|---|---|---|
| `CLAUDE.md` (or `AGENTS.md`) | lasting knowledge: what the workspace is, its layout, how to build and test, gotchas, and where it differs from this handbook. Loaded into every session, so it stays an index: the details go in `agent/notes/`, linked with when to read them | [workspace-CLAUDE.md](../templates/workspace-CLAUDE.md) |
| `PLAN.md` | the index: the goal, "Now" and "Then", every other task by link, what is waiting | [workspace-PLAN.md](../templates/workspace-PLAN.md) |
| `agent/tasks/` | a [task file](#task-files) for each task | [workspace-task.md](../templates/workspace-task.md) |
| `agent/sprints/` | a [sprint note](#sprint-notes) for each round of work that needs one, read on demand; created with the first one | [workspace-sprint.md](../templates/workspace-sprint.md) |
| `agent/notes/` | notes and research: procedures, cached documentation, investigation logs, and the project's [working rules](#the-agents-own-memory) (`working-rules.md`); created when needed | |
| `agent/scripts/` | scripts written along the way; created when needed | |

The folder is `agent/`, not `.agent/`: a hidden folder is hidden in Finder and skipped by Obsidian too.

1. **Pick the root.** Prefer a **workspace folder** around the repository, `~/development/<name>/<repo>/`. It is a private place for what doesn't belong in the repository:
   - tools that aren't the project's own: built runtimes, virtual environments, pinned command-line tools (often machine-specific and not relocatable, so they stay where they were built, e.g. a `tools/` folder at the root, untracked)
   - notes and research: cached documentation, investigation logs
   - scripts written along the way
   - agent instructions and a plan that mention local paths, or that shouldn't be in a public repository's history
   - room for related repositories, or [worktrees](https://git-scm.com/docs/git-worktree) for other branches (`git worktree add ../<repo>-dev dev`: a second checkout that shares one history, instead of a second clone)

   Anything other contributors need, such as build steps, tests, or a script the project depends on, still belongs in the repository and its docs; the workspace's instructions link there instead of repeating it.

   The repository alone is enough for a small project whose tools come from its own package manager and that has no local notes: then the workspace root is the repository root.
2. **Always start sessions there.** Claude Code reads `CLAUDE.md` from the folder a session starts in and its parents, and keeps its memory per starting folder: a session started in a subfolder misses both.
3. **Write the agent instructions** from the template. Keep them short and factual: what every session needs (layout, working rules, the build and test commands, gotchas that fail silently) stays in them, and anything needed only for some tasks (a build system in depth, test setups, helper scripts) goes in a note in `agent/notes/`, linked from a "Notes" table that says when to read it; a workspace's instructions win where they disagree with this handbook, so list the differences (such as a default branch other than `main`) there. Running Claude Code's `/init` gives a first draft to trim.
4. **Write the plan** from the templates: `PLAN.md` with the goal and the tasks, and a task file for the first task, linked under "Now".
5. **Put them under version control,** so a bad edit can be undone and a lost disk doesn't lose them:
   - *The repository as the workspace:* commit them with the code. If the repository is public, keep identifying details out of them (see [Public Repositories](git-workflow.md#public-repositories)), or list `PLAN.md` and `agent/` in `.gitignore` if they hold notes that shouldn't be published.
   - *A workspace folder:* make the folder itself a small **private** repository that tracks only its own files and ignores the checkouts and installed tools:

     ```gitignore
     # track only the workspace's own files, not the checkouts or installed tools
     /*
     !/.gitignore
     !/CLAUDE.md
     !/PLAN.md
     !/agent/
     ```

     It is kept in one of two ways, and its instructions say which:

     - **Synced** (the usual way): it has one remote, `origin`, a **private** repository on the server that holds my private code. That is GitLab; GitHub is for the open source, so a workspace never goes there. The remote carries the plan and the notes from one machine to another (see [Working on More Than One Machine](#working-on-more-than-one-machine)) and covers a lost disk.
     - **Local only:** no remote. For a workspace that stays on one machine, such as one tied to hardware attached to it. The repository is then for undoing a bad edit and reviewing how the files changed, and the machine's backup covers a lost disk.

     Either way a **push guard** keeps the notes private. A stray `git push` to the wrong place, or a GUI client's "Publish" button, would otherwise put them on a public server. Save this as `.git/hooks/pre-push` and make it executable (`chmod +x`):

     ```sh
     #!/bin/sh
     # This workspace is private: refuse every push but one to its own private remote.
     # $2 is the address being pushed to; workspace.pushUrl is unset in a local-only workspace.
     allowed=$(git config --get workspace.pushUrl)
     [ -n "$allowed" ] && [ "$2" = "$allowed" ] && exit 0
     echo "push refused: this workspace pushes only to ${allowed:-nowhere (it is local only)} (.git/hooks/pre-push)" >&2
     exit 1
     ```

     A synced workspace names its remote to the guard:

     ```sh
     git config workspace.pushUrl "$(git remote get-url --push origin)"
     ```

     Without that setting the guard refuses every push, which is what a local-only workspace needs. With it, the guard still refuses any other remote, including one added later, and a push to another address. It does not stop `git push --no-verify`. Neither the hook nor the setting is versioned, so a fresh clone has neither until they are added. The checkouts inside the folder have their own `.git` and push as before.

     A synced workspace is on a server, private or not: keep passwords, tokens and keys out of its files, as out of any repository.

6. **Start** the first session with "let's continue with PLAN.md" (in Claude Code, `/next`), then follow the [normal flow](#normal-flow): Start, Research, Perform, Verify, Finalize.

### Working on More Than One Machine

A workspace can move between machines: a desktop for most of the work, a laptop for travel or for a job that needs it. Two things carry it: the workspace's own repository, through its private remote, and each checkout, through its own remote. Nothing else travels:

| does not travel | so |
|---|---|
| uncommitted changes, and branches that are not pushed | finish the work before leaving a machine (below) |
| installed tools, dependencies and build output | install and build again on each machine, per the workspace's instructions |
| the push guard and its setting | add them on each machine |
| an agent's own memory of the folder | promote it into the workspace's files at each checkpoint (see [The Agent's Own Memory](#the-agents-own-memory)) |
| this handbook, and the agent setup that points to it | set it up on each machine, per the [README](../README.md#setup) |

**Keep the same path on every machine** (`~/development/<name>/`), so the paths in the instructions and notes hold on both. A fact that is true of one machine only (a port in use, a device attached, an installed version) names the machine: "on the laptop".

**Leaving a machine.** Finish first: every change in progress is completed, reviewed, merged and pushed, and the workspace repository is committed and pushed, so the workspace is ready for `/clear` and `/next` on either machine. That is the ordinary checkpoint (see [Normal Flow](#normal-flow)), and it is the rule.

Only when the work cannot be finished in time: commit it on its branch and push the branch to the checkout's own remote, update the task's status block, and commit and push the workspace repository. The unmerged branch of an open source checkout is then on GitHub and public: scan it for identifying details first (see [Public Repositories](git-workflow.md#public-repositories)), and delete it on the remote after the merge. Each push waits for my go-ahead, as always.

**Arriving at a machine.** In the workspace folder, `git pull --ff-only`. In each checkout, `git fetch`, and check out the task's branch. Install again if the dependencies changed. In Claude Code, [`/next`](../skills/next/SKILL.md) does the first two. A workspace repository with commits on both machines does not fast-forward: stop and look at both sides before joining them.

**The first time on a machine:**

1. Clone the workspace repository to the same path: `git clone <address> ~/development/<name>`.
2. Add the push guard and its setting, from step 5 above.
3. Clone each checkout into the folder the instructions' "Layout" names, from the address given there.
4. Install the tools and dependencies, per the instructions.
5. Start the session there, as on the other machine.

### Design Documents and Roadmaps

A project often starts with a design worked out elsewhere, such as a chat or a shared document: architecture, decisions, a roadmap of phases. It is not the plan. The plan says what is being done now; the design says what the project is and where it is going. Each part has its place in the repository (see the [documentation guidelines](documentation.md)):

| Part of the design | Goes to |
|---|---|
| How it works: structure, flow, components | `docs/architecture.md` |
| Each important decision, with its reasons and the options rejected | an ADR in `docs/decisions/` |
| Decided features, by phase | a GitHub milestone per phase, an issue per feature (`### Goal`, `### Why`, details); see [Planned Work](git-workflow.md#planned-work) |
| Ideas not decided, research | "Possible Future Changes" in `docs/development.md` |
| The current phase's next steps | `PLAN.md` and its task files, linking the milestone and issues |

While the design is still changing, it can stay where it is: the workspace's `CLAUDE.md` links it and says when to read it (before design work, not every session). Once implementation starts, moving it into the repository is the first task in the plan, so that the code and its design are versioned together and readable by any agent or contributor. The old document then gets a note at the top that it has moved.

## Normal Flow

Every task goes through five phases. Each phase ends with a **handoff**: what the next phase needs is written down, so the session can be cleared there without losing anything.

| Phase | What happens | Handoff |
|---|---|---|
| **Start** | Read the agent instructions and `PLAN.md`'s "Now" and "Then". Take the task under "Now", or the top of "Then", and read its task file. Create the branch, per the [git workflow](git-workflow.md), and a sprint note if the task needs one. In Claude Code, [`/next`](../skills/next/SKILL.md) does this and resumes where the status block says. | The task file's frontmatter names the branch and the phase. |
| **Research** | Explore the code, reproduce the problem, try fixes in a scratch copy. | Findings, with their evidence, in the [sprint note](#sprint-notes); the chosen approach and its assumptions, and the decisions, in the task file; bugs found but not fixed as plan entries or Known Issues. |
| **Perform** | Write the code and the docs. Update every doc the change affects: the repository's docs and changelog, the agent instructions for anything learned about the workspace, plan entries for anything found but not done. | Uncommitted changes; Done and Left in the task's status block. |
| **Verify** | Check the change the way the project checks things: tests, the Quick Start, the examples. The workspace's agent instructions say how. | The results in the sprint note and the status block. |
| **Finalize** | I review the change; the agent checkpoints and says so (below). | Commits, the plan files updated, the next task named. |

In coding work, Perform and Verify alternate in small loops (change, test, change again) until the change works; a clear point between them is rare. A small task runs through the phases in one go, without a sprint note, and a tiny one without a task file.

**Finalize** in detail:

1. **Review:** the agent leaves the change uncommitted and lists what changed, repository by repository; I review it in the editor.
2. **Checkpoint:** once I approve, commit, merge, and push (see the [git workflow](git-workflow.md)). Update the plan: mark the task file done (or, if the task goes on, update its status block for the next round), move the next task up to "Now" in `PLAN.md`, and refill "Then" from the tasks below it. Mark the sprint note done. Check the conversation for anything not yet saved, and promote anything new in the agent's own memory (see [The Agent's Own Memory](#the-agents-own-memory)). Commit the workspace repository, if the workspace folder is one, and push it if it is synced. Nothing should exist only in the conversation. In Claude Code, [`/allthethings`](../skills/allthethings/SKILL.md) does this step and the next.
3. **Say so:** the agent tells me that everything is saved and `/clear` is safe, names the task the next session would take (the new "Now" in `PLAN.md`) in a line or two, and reminds me how to start it: "let's continue with PLAN.md", or `/next`. Seeing the next task first gives me the chance to reorder the plan before starting it.
4. **`/clear`** and start the next session at Start.

### The Status Block

The task file's `## Status` section, together with its frontmatter, says where the task stands. It is updated at the end of each phase, so that "let's continue with PLAN.md" resumes in the right phase:

```markdown
---
status: active
phase: <Start, Research, Perform, Verify or Finalize>
repos: [<repository>]
branch: <branch>
---
…
## Status

- Done: <what is finished so far, and whether it is uncommitted or committed>
- Left: <what remains>
- Decisions: <choices made along the way that the rest depends on>
- Next step: <the first thing the next session does>
- Sprint: [<file>](../sprints/<file>.md)
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

Before suggesting a clear, the agent checks that the handoff is complete: the task file's status block is current, the sprint note has what the next phase needs, and nothing exists only in the conversation. It suggests the clear with the reason; it's my call, and I run `/clear` (or `/compact <what to keep>` to keep a summary).

### Sprint Notes

A task file says what we're doing; a **sprint note** records what happened in one round of work on it: the evidence behind the approach, and what was tried. It is kept apart so that it is read only when needed.

A round is usually one branch (or one set of branches, for a change across several repositories), from its creation to its merge. A small task has one round, or none worth a note; a large one has many. Each note is one file in `agent/sprints/`, named `<yyyy-mm-dd-hhmm>-<slug>.md`, started from [the template](../templates/workspace-sprint.md).

- **One section per phase,** added to at each handoff, never rewritten; the frontmatter (`task`, `phase`, `status`, `branch`, `repos`) links the task and says where the round stands.
- **Research records evidence, not only conclusions:** `file:line` locations, the commands that reproduce the problem and their output (trimmed), tracebacks, the approaches considered and why they were rejected, and the **assumptions** the chosen approach rests on.
- **Perform and Verify record failed attempts** and why they failed, so they aren't tried again, and the test results.
- **Findings go up, evidence stays down.** What the task needs from a round (a decision, a new step, a bug to fix later) is written into the task file or `PLAN.md`; the traceback behind it stays in the sprint note.
- **When a fix fails,** reload the sprint note instead of researching again, and recheck its assumptions first: a failed fix usually means one of them was wrong.
- **Read on demand only,** never at session start; the task's status block links to it. At the round's end its status becomes `done`, and it stays as the record of how the work was done.

Task files and sprint notes are plain Markdown with Markdown links (not wikilinks), so they read in any editor, and the workspace folder can be opened as an [Obsidian](https://obsidian.md) vault (with the checkouts excluded).

## Save As You Go

Write things down when they come up, not at the end, so a checkpoint only has to confirm nothing is left:

| What | Where |
|---|---|
| An important technical decision about how the software works, with its reasons and the options rejected | an ADR in the repository's `docs/decisions/` (see [Decision Records](documentation.md#decision-records-docsdecisions)), or a plan entry to write one. Smaller choices need no record beyond the commit message; when unsure, the agent asks me |
| A preference about how I like things done | the relevant dev-handbook guideline |
| A rule for this project only, from a correction or a choice I made | the workspace's [working rules](#the-agents-own-memory) |
| A gotcha about the workspace: tools, builds, quirks, commands that work | the agent instructions (`CLAUDE.md` or `AGENTS.md`), or the note in `agent/notes/` they link for that topic |
| A bug or improvement found but not fixed | a plan entry (a GitHub issue if others should see it, or the project is winding down), or "Possible Future Changes" if undecided |
| A useful script written along the way | the workspace's `agent/scripts/`, noted in its `README.md` (or in the agent instructions) |

Anything written to a temporary scratch folder is lost with the session: move what's worth keeping.

### The Agent's Own Memory

Some harnesses keep a memory of their own: in Claude Code, `~/.claude/projects/<path>/memory/`, one per starting folder. It is useful within a machine, but it stays on that machine and that user account, and no other harness reads it. So it is a scratchpad: what must last is promoted into files, and the memory entry is then removed, so the rule doesn't live in two places that drift apart.

A memory entry goes to the first place that fits:

1. **The repository's docs,** if it is about the project and any contributor needs it: an ADR or its amendment, the configuration reference, the contributor guide.
2. **The agent instructions' "Working Rules",** if it is short and every session needs it.
3. **`agent/notes/working-rules.md`,** for the rest: the project's rules for agents that its docs leave out. Each rule names where it is documented, if anywhere, and adds what the docs don't say: why (the correction or the choice behind it), how to apply it (the tests to add, the doc to keep in step), and what not to propose. It is linked from the instructions' "Notes" table, with when to read it.
4. **The dev-handbook,** if it is about how I work everywhere, not this project.

Facts about the work in progress go in the task file, as always. Personal details stay out of all of these as out of any file: the workspace repository may be private, but the checkouts are often public.

The memory is checked at every checkpoint (see [Finalize](#normal-flow)): anything new is promoted, then removed from the memory. Because promoted entries are removed, whatever is left is new, so the check reads only the memory's index (in Claude Code, `MEMORY.md`, which is loaded into every session anyway) and opens just the entries it lists; an empty index means nothing to do. Never read the whole memory to find what changed.

## Emergency Mode

Sometimes a project is in bad shape: years out of date, broken builds, many problems that depend on each other. Fixing one thing at a time with a cleared session in between would mean rediscovering the same context over and over, so one longer session is fine:

- **Declare it:** say that this session is a cleanup, and start the plan files right away.
- **Keep writing things down as you go** ([save as you go](#save-as-you-go) applies even more). The plan files and agent instructions grow during the session, so the session could still be cleared if it had to be.
- **Still one branch per change,** tested, merged in dependency order.
- **Exit** once every remaining problem can be described in a plan entry and done in a session of its own. Then checkpoint, trim the plan to its shape (`PLAN.md` as an index, a task file per task), and `/clear`. From then on, normal flow.
