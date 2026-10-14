# Changes to the Workspace Layout

Changes to how a workspace is laid out (its files, folders and plan format), newest first, each with how to tell whether a workspace has it and how to update one that doesn't. Changes to the guidelines' wording aren't listed; the commit history has them.

In Claude Code, [`/init-workspace`](skills/init-workspace/SKILL.md) run in an existing workspace checks each change below and proposes the updates it's missing. By hand: go down the list until a change's check passes; apply the ones above it, oldest first.

## 2026-10-13: The Workspace Repository Synced to a Private Remote

A workspace folder's own repository is no longer local only by rule. The usual way is now **synced**: one remote, `origin`, a private repository on the server for private code (GitLab, never GitHub), so the work can move between machines. **Local only** remains for a workspace that stays on one machine. The push guard changes with it: it lets through a push to the remote named in `git config workspace.pushUrl` and refuses every other, and with nothing named it refuses all, as before. See step 5 of [Setting Up a Workspace](guidelines/agent-workflow.md#setting-up-a-workspace) and [Working on More Than One Machine](guidelines/agent-workflow.md#working-on-more-than-one-machine). Not needed when the workspace is the repository itself.

**Has it:** `.git/hooks/pre-push` in the workspace folder is executable and reads `workspace.pushUrl`. A fresh clone on another machine fails this until the guard is added there.

**Update:**

1. **Ask which way** this workspace is kept: synced, or local only.
2. **Replace the hook** with the one from the guideline, and `chmod +x` it. Check that `git config core.hooksPath` prints nothing. A local-only workspace is done here, apart from step 5.
3. **Add the remote,** for a synced workspace. The user creates the private repository and gives its address: `git remote add origin <address>`, then `git config workspace.pushUrl "$(git remote get-url --push origin)"`. If the folder is a fresh clone, the remote is there already and only the setting is missing.
4. **Look before the first push:** search the tracked files for passwords, tokens and keys (`git grep -inE 'password|secret|token|api[_-]?key'`), and their history if anything was ever committed and removed. Report what is found. Push (`git push -u origin <default-branch>`) only on the user's go-ahead.
5. **Update the instructions:** `CLAUDE.md` says which way the workspace is kept, in place of any line calling it local only or saying the hook refuses every push, and its "Layout" gives the address each checkout is cloned from.

## 2026-10-03: A Push Guard on the Workspace Repository

A workspace folder's own repository is local only, and a `pre-push` hook now enforces it: a stray `git push` or a GUI client's "Publish" button would otherwise put private notes on a server. See step 5 of [Setting Up a Workspace](guidelines/agent-workflow.md#setting-up-a-workspace). Not needed when the workspace is the repository itself. Changed by [2026-10-13](#2026-10-13-the-workspace-repository-synced-to-a-private-remote): a workspace that needs both takes the hook and the remote from that one.

**Has it:** `.git/hooks/pre-push` in the workspace folder exists, is executable, and exits 1; `git remote` prints nothing.

**Update:**

1. **Check for a remote** with `git remote -v`. If there is one, ask before removing it (`git remote remove <name>`): the repository may already be on a server, and deleting it there is the user's call.
2. **Add the hook** from the guideline as `.git/hooks/pre-push`, and `chmod +x` it.
3. **Check** that `git config core.hooksPath` prints nothing: if it is set, hooks are read from that folder and the one in `.git/hooks` is ignored.

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
