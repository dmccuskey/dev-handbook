# Changes to the Workspace Layout

Changes to how a workspace is laid out (its files, folders and plan format), newest first, each with how to tell whether a workspace has it and how to update one that doesn't. Changes to the guidelines' wording aren't listed; the commit history has them.

In Claude Code, [`/init-workspace`](skills/init-workspace/SKILL.md) run in an existing workspace checks each change below and proposes the updates it's missing. By hand: go down the list until a change's check passes; apply the ones above it, oldest first.

## 2026-10-19: Ports From a Block in the Registry

Each project now claims a block of ten ports in the registry, in the handbook's own workspace (see [The Handbook's Own Workspace](guidelines/agent-workflow.md#the-handbooks-own-workspace)). Every port it uses goes in that block, including its dev services' ports, instead of the tools' defaults. Not needed when the handbook sits on its own, without the workspace and its `registry/`.

**Has it:** the workspace's `CLAUDE.md` names the project's block, and it matches the project's row in the registry's `ports.md`. A project that runs no server or dev service passes too, once its `CLAUDE.md` says so.

**Update:** ask first, since it changes the project's config.

1. **Pull the registry:** `git -C ~/development/dev-handbook-workspace pull --ff-only`. Read its `README.md`, then look up the project in `ports.md`: its block, and any of its rows under "Not Yet in Their Block".
2. **Find every port the project uses:** its configs (`vite.config.*`, `vue.config.js`, `package.json` scripts, `.env.example`, `docker-compose.yml`, `Procfile`), its server code, and the workspace's `CLAUDE.md`, notes and scripts. Include each dev service left on its default port, and anything it connects to on the machine. Report what is found, and ask about anything unclear.
3. **Reserve a block** if the project has none, per the registry's `README.md`.
4. **Move each port into the block,** on a branch in the checkout. Update the workspace's notes and scripts that name the old ports as well. A service installed on the machine for everything on it, such as a database run as a system service, keeps its default port: check that it is listed under "Machine Services".
5. **Write the block into `CLAUDE.md`,** with which port does what. Usually this goes under "Build and Test", where the serve commands are.
6. **Update the registry:** add the block's row if it is new, and remove the project's rows under "Not Yet in Their Block". Leave the edit uncommitted there for review, like the project's.
7. **Check** that each server starts on its new port and that the tests pass.

## 2026-10-19: The Handbook in Its Own Workspace

The handbook's checkout moves from `~/development/dev-handbook/` into a workspace folder of its own, `~/development/dev-handbook-workspace/dev-handbook/`. The workspace holds the handbook's plan and notes, and `registry/`, the claims on what projects share, such as ports. See [The Handbook's Own Workspace](guidelines/agent-workflow.md#the-handbooks-own-workspace). This is a change to each machine's setup, not to each workspace: do it once per machine.

**Has it:** `~/.claude/CLAUDE.md` imports `@~/development/dev-handbook-workspace/dev-handbook/AGENTS.md`, `ls -L ~/.claude/skills/next/SKILL.md` succeeds, and `readlink ~/.claude/skills/next` names the new path. With Codex, `readlink ~/.codex/AGENTS.md` names it too.

**Update:**

1. **Check the old checkout** for uncommitted changes and unpushed branches (`git status`, `git log --branches --not --remotes`). Finish or push them first.
2. **Get the workspace:** if it is already synced from another machine, clone it to `~/development/dev-handbook-workspace/` and add its push guard (see [Working on More Than One Machine](guidelines/agent-workflow.md#working-on-more-than-one-machine)). Without one, create the folder.
3. **Move the checkout** into it: `mv ~/development/dev-handbook ~/development/dev-handbook-workspace/dev-handbook`. Moving the folder a session runs in breaks the session: run it from another folder.
4. **Fix the paths** that named the old checkout, as in the [Setup](README.md#setup):
   - `~/.claude/CLAUDE.md`: the import line.
   - `~/.claude/skills/`: each link into the handbook's `skills/` (`ln -sfn <new path>/skills/<name> ~/.claude/skills/<name>`). Leave real folders alone.
   - `~/.codex/AGENTS.md`, if it exists: link it again.
   - Any other harness's global instructions that name the old path.
5. **Check** the "Has it" test, then start a new session: the handbook's index and the skills load.
6. **Promote the agent's memory** of the old folder, if sessions ran there, per the [2026-10-15](#2026-10-15-working-rules-promoted-from-the-agents-memory) change: it is keyed by the folder and stays behind.

## 2026-10-16: The Workspace Folder Named `<name>-workspace`, Kept Flat

A workspace folder is now named `<name>-workspace` (`~/development/<name>-workspace/`), and holds its own files and every checkout side by side: no group folders, no symlinks. The suffix tells it apart from the repositories inside. See step 1 of [Setting Up a Workspace](guidelines/agent-workflow.md#setting-up-a-workspace). Not needed when the workspace is the repository itself. Optional for a workspace that works as it is: renaming one is a move, best done when it moves anyway (to another machine, say).

**Has it:** the workspace folder's name ends in `-workspace`, and every checkout is directly inside it (`find . -mindepth 3 -maxdepth 3 -name .git` and `find . -maxdepth 1 -type l` list nothing).

**Update:** ask first; it moves every checkout.

1. **Promote the agent's memory** of the folder, per the 2026-10-15 change: the memory is keyed by the folder, so it is left behind.
2. **Finish or push** the work in progress, as when leaving a machine (see [Working on More Than One Machine](guidelines/agent-workflow.md#working-on-more-than-one-machine)).
3. **Move** the workspace repository and each checkout into `~/development/<name>-workspace/` with `mv`, which keeps their history, branches and remotes. Replace each symlink with the checkout it pointed to. Moving the folder the session runs in breaks the session: give the user the commands to run, and start the next session in the new folder.
4. **Rebuild** the installed tools that aren't relocatable (virtual environments, built runtimes), then run the tests.
5. **Fix the paths:** the instructions, the live notes and the scripts name the new folder; done tasks and old sprint notes keep the old one as history. On another machine, clone the workspace to the new path and set it up as the first time (see [Working on More Than One Machine](guidelines/agent-workflow.md#working-on-more-than-one-machine)).

## 2026-10-15: Working Rules Promoted From the Agent's Memory

An agent's own memory (in Claude Code, `~/.claude/projects/<path>/memory/`) stays on one machine and one user account, and no other harness reads it. It is now a scratchpad: at each checkpoint, what it holds is promoted into files and removed from it. The project's rules for agents that its docs leave out go in `agent/notes/working-rules.md`, linked from the instructions' "Notes" table. See [The Agent's Own Memory](guidelines/agent-workflow.md#the-agents-own-memory).

**Has it:** the agent's memory for the workspace is empty, or holds only entries already written down; and if there is an `agent/notes/working-rules.md`, `CLAUDE.md` links it under "Notes".

**Update:**

1. **Read the memory** for the workspace, every entry.
2. **Check each against what is written down:** the repository's docs, `CLAUDE.md`, `agent/notes/`, and the dev-handbook. An entry that is fully covered needs nothing more.
3. **Promote the rest,** per [The Agent's Own Memory](guidelines/agent-workflow.md#the-agents-own-memory): to the repository's docs (on a branch, as any change there), the `CLAUDE.md` "Working Rules", `agent/notes/working-rules.md`, or the dev-handbook. In `working-rules.md`, one section per rule: where the rule is documented, then why, how to apply it, and what not to propose. Leave out personal details, such as account names and hosts.
4. **Link the note** from the "Notes" table in `CLAUDE.md`, with when to read it, and check that its links resolve.
5. **Clear the memory,** once the user has reviewed the promoted text: remove each promoted or covered entry and its line in the memory's index (`MEMORY.md` in Claude Code). Ask before removing anything else. Do this on each machine that has a memory for the workspace.

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
