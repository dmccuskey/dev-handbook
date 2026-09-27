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

## Setting Up a Workspace

A workspace is the folder where agent sessions start: usually a folder that holds the repository (or several related ones), or, for a small project, the repository itself. This handbook needs no setup per workspace: every harness loads it from its global instructions (see the [README](../README.md#setup)). A workspace needs only its own two files, at its root. In Claude Code, [`/init-workspace`](../skills/init-workspace/SKILL.md) looks at the folder, proposes a setup along the steps below, and sets it up once approved.

| File | Holds | Template |
|---|---|---|
| `CLAUDE.md` (or `AGENTS.md`) | lasting knowledge: what the workspace is, its layout, how to build and test, gotchas, and where it differs from this handbook | [workspace-CLAUDE.md](../templates/workspace-CLAUDE.md) |
| `PLAN.md` | the state of the work: Now, Next, Waiting | [workspace-PLAN.md](../templates/workspace-PLAN.md) |

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
4. **Write the plan file** from the template, with the first task under "Now".
5. **Put both under version control,** so a bad edit can be undone and a lost disk doesn't lose them:
   - *The repository as the workspace:* commit them with the code. If the repository is public, keep identifying details out of both (see [Public Repositories](git-workflow.md#public-repositories)), or list `PLAN.md` in `.gitignore` if it holds notes that shouldn't be published.
   - *A workspace folder:* make the folder itself a small **private** repository that tracks only its own files and ignores the checkouts and installed tools. Its instructions describe the local layout, so it stays private:

     ```gitignore
     # track only the workspace's own files, not the checkouts
     /*
     !/.gitignore
     !/CLAUDE.md
     !/PLAN.md
     !/notes/
     !/scripts/
     ```

6. **Start** the first session with "let's continue with PLAN.md", then follow the [normal flow](#normal-flow).

### Design Documents and Roadmaps

A project often starts with a design worked out elsewhere, such as a chat or a shared document: architecture, decisions, a roadmap of phases. It is not the plan file. The plan says what is being done now; the design says what the project is and where it is going. Each part has its place in the repository (see the [documentation guidelines](documentation.md)):

| Part of the design | Goes to |
|---|---|
| How it works: structure, flow, components | `docs/architecture.md` |
| Each important decision, with its reasons and the options rejected | an ADR in `docs/decisions/` |
| Decided features, by phase | a GitHub milestone per phase, an issue per feature (`### Goal`, `### Why`, details); see [Planned Work](git-workflow.md#planned-work) |
| Ideas not decided, research | "Possible Future Changes" in `docs/development.md` |
| The current phase's next steps | `PLAN.md`: "Now" and "Next", linking the milestone and issues |

While the design is still changing, it can stay where it is: the workspace's `CLAUDE.md` links it and says when to read it (before design work, not every session). Once implementation starts, moving it into the repository is the first task in the plan, so that the code and its design are versioned together and readable by any agent or contributor. The old document then gets a note at the top that it has moved.

## Normal Flow

1. **Start:** read the agent instructions and `PLAN.md`. Take the task under "Now", or the top of "Next".
2. **Work:** on a branch, per the [git workflow](git-workflow.md). Test. [Save as you go](#save-as-you-go).
3. **Update the docs** the change affects: the repository's docs and changelog, the agent instructions for anything learned about the workspace, and plan entries for anything found but not done.
4. **Review:** the agent leaves the change uncommitted and lists what changed, repository by repository; I review it in the editor.
5. **Checkpoint:** once I approve, commit, merge, and push (see the [git workflow](git-workflow.md)). Rewrite "Now" to the next task. Check the conversation for anything not yet saved. Nothing should exist only in the conversation.
6. **Say so:** the agent tells me that everything is saved and `/clear` is safe, names the task the next session would take from the plan file (the "Now" task, or the top of "Next") in a line or two, and reminds me how to start it: "let's continue with PLAN.md". Seeing the next task first gives me the chance to reorder the plan before starting it.
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
