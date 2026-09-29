# Changes to the Workspace Layout

Changes to how a workspace is laid out (its files, folders and plan format), newest first, each with how to tell whether a workspace has it and how to update one that doesn't. Changes to the guidelines' wording aren't listed; the commit history has them.

In Claude Code, [`/init-workspace`](skills/init-workspace/SKILL.md) run in an existing workspace checks each change below and proposes the updates it's missing. By hand: go down the list until a change's check passes; apply the ones above it, oldest first.

## 2026-09-29: `CLAUDE.md` as an Index

The agent instructions are loaded into every session, so they keep only what every session needs (layout, working rules, the build and test commands, gotchas that fail silently) and link the rest: notes in `agent/notes/` for detail needed only for some tasks, listed in a "Notes" table with when to read each. In Claude Code, sessions start with [`/next`](skills/next/SKILL.md). See [Setting Up a Workspace](guidelines/agent-workflow.md#setting-up-a-workspace).

**Has it:** `CLAUDE.md` has a "Notes" section, or is short enough not to need one (under about 4 KB).

**Update:**

1. **Sort** each section of `CLAUDE.md`: needed in every session (stays), or only for some tasks (moves). Build systems in depth, test setups, installed tools, helper scripts and the gotchas that belong to one of them usually move.
2. **Move** the details into notes in `agent/notes/`, one per topic (e.g. `build.md`, `testing.md`), and a long helper-script list into `agent/scripts/README.md`. Keep the wording; fix relative links for the new location.
3. **Link** them from a "Notes" table at the end of `CLAUDE.md`: each note, and when to read it. Leave a one-line summary in place where a section shrank (e.g. the build command, with "details: build.md").
4. **Check** that nothing was lost (every fact in the old `CLAUDE.md` is in it or a note) and that every link resolves.

## 2026-09-29: Task Files, Sprint Notes and `agent/`

`PLAN.md` becomes an index ("Now", "Then", then any structure, "Waiting"), one line per task linking a task file in `agent/tasks/`. `NEXT.md` goes away. Sprint notes become one per round of work (usually one branch), in `agent/sprints/`, with a `task:` field. The workspace's own folders move into `agent/`. See [The Plan Files](guidelines/agent-workflow.md#the-plan-files).

**Has it:** `agent/tasks/` exists and there is no `NEXT.md` at the root.

**Update:**

1. **Create `agent/`** and move the workspace's own folders into it: `notes/`, `sprints/`, and scripts (`scripts/`, or `tools/scripts/`) to `agent/scripts/`. Installed tools (runtimes, virtual environments) stay where they are: they usually can't be moved, only rebuilt. Use `git mv` in a workspace repository.
2. **Fix what the move breaks:** scripts that find files relative to their own location (check the depth: `tools/scripts/` and `agent/scripts/` are both two levels down, `scripts/` one), and links and paths in the notes, the sprint notes, `CLAUDE.md`, and skills or scripts elsewhere that name the old paths.
3. **A task file per task**, from [templates/workspace-task.md](templates/workspace-task.md): the task in "Now" gets its status block as `## Status` (Phase, Where, repositories and branch go in the frontmatter); each task in "Then" and each queued task in `PLAN.md` with more than a line of detail gets a file with that detail. A long note that belongs to one task can become its task file. Tiny tasks can stay plain lines.
4. **Rewrite `PLAN.md` as the index,** from [templates/workspace-PLAN.md](templates/workspace-PLAN.md): the goal, "Now", "Then", the rest in whatever structure fits, "Waiting". Procedures and status lists that aren't tasks (a per-repository checklist, a list of what's done) go in a note in `agent/notes/`, linked from the section they belong to. Then delete `NEXT.md`.
5. **Existing sprint notes** can stay as they are (they are records); add `task:` to one still in progress.
6. **`.gitignore`** of a workspace repository: `/*`, then `!/.gitignore`, `!/CLAUDE.md`, `!/PLAN.md`, `!/agent/`, plus anything else the workspace tracks at its root. Check with `git status --short` that nothing else became tracked or untracked.
7. **`CLAUDE.md`:** the line saying what to work on points to `PLAN.md` ("Now", then the task file); the layout and helper-script paths name `agent/`.
8. **Check** that every relative link in `PLAN.md`, the task files, the notes and `CLAUDE.md` resolves, and look in the agent's memory for the workspace (in Claude Code, `~/.claude/projects/<path>/memory/`) for mentions of `NEXT.md` or the old paths.

## 2026-09-27: Session Phases and Sprint Notes

Tasks go through Start, Research, Perform, Verify and Finalize; the status block has a Phase and Decisions; a task with a Research phase gets a sprint note. See [Normal Flow](guidelines/agent-workflow.md#normal-flow).

**Has it:** `CLAUDE.md` says how the Verify phase checks a change (a Verify line, usually under "Build and Test" or "Testing").

**Update:** add a Verify line to `CLAUDE.md`: how a change is checked here (tests, the Quick Start, the browser). Sprint notes are created as tasks need them.

## 2026-09-27: `NEXT.md`

The task in progress and the next few moved from `PLAN.md` into `NEXT.md`. Replaced by the 2026-09-29 change: a workspace without it goes straight to that one, taking "Now" and "Next" from `PLAN.md`.

## 2026-09-26: The Workspace Folder as a Repository

A workspace folder around one or more checkouts is itself a small private git repository, local only, tracking only its own files. See [Setting Up a Workspace](guidelines/agent-workflow.md#setting-up-a-workspace).

**Has it:** `git rev-parse --show-toplevel` in the workspace folder prints that folder (not needed when the workspace is the repository itself).

**Update:** `git init -b main`, the `.gitignore` from the 2026-09-29 change, and a first commit of the workspace's own files. No remote.
