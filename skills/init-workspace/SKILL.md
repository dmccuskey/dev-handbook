---
name: init-workspace
description: Set up the current folder as an agent workspace (CLAUDE.md, PLAN.md, agent/ folder, workspace folder or repository alone) per the dev-handbook, or update an existing workspace to the current layout (per the handbook's CHANGES.md) - propose first, change only after the user approves. Only when the user types /init-workspace.
disable-model-invocation: true
argument-hint: "[what the first task is]"
---

# Init Workspace

Set up the folder this session started in as a workspace, per the dev-handbook's agent workflow. Work in two phases: **propose, then stop**; set up only after the user approves the proposal (they may discuss and change it first).

If the user gave an argument, it describes the first task.

**If the folder is already a workspace** (a `CLAUDE.md` or `AGENTS.md` and a `PLAN.md` at its root), don't set it up again: follow [Updating a Workspace](#updating-a-workspace) instead.

## Phase 1: Look and Propose

Change nothing in this phase: read and run read-only commands only.

1. **Read the handbook:** "Setting Up a Workspace" in `~/development/dev-handbook/guidelines/agent-workflow.md`, and the templates `~/development/dev-handbook/templates/workspace-CLAUDE.md`, `workspace-PLAN.md` and `workspace-task.md`.
2. **Look at the folder:**
   - Is it a git repository (`git rev-parse --show-toplevel`), a folder holding one or more checkouts, or empty? Is it inside another workspace (a `CLAUDE.md`, `PLAN.md` or `agent/` in a parent folder)?
   - For each repository: its remote and whether it is public (`gh repo view --json visibility,description` when it's on GitHub), its default branch, its branches, and whether the working tree is clean.
   - What the project is: README, `docs/`, languages, package managers and their files (`package.json`, `pyproject.toml`, `Gemfile`, ...), build and test commands, CI config.
   - What already exists: `CLAUDE.md`, `AGENTS.md`, `NEXT.md`, `PLAN.md`, `agent/`, notes, scripts, installed tools, several clones of the same repository (candidates for worktrees).
   - Design documents: architecture, decisions or a roadmap, in the repository or elsewhere. Ask whether one exists outside the folder (a chat, a shared document); if the user gives a link, read it (a claude.ai artifact link may be a Claude Doc: read it with the docs tools) and skim it for what affects the setup, such as the tools, repositories and branches it plans.
3. **Ask about the first task** if neither the argument nor the folder makes it clear. One question, not a questionnaire.
4. **Write the proposal**, short, in the conversation:
   - **Root:** a workspace folder around the repository, or the repository alone, and why (the handbook's criteria: tools that aren't the project's own, notes, local paths, a public repository, related repositories). When unsure how big the project will get, say so and propose the smaller option: moving to a workspace folder later is easy (create the folder, move the checkout into it, move `CLAUDE.md`, `PLAN.md` and `agent/` up a level, and start sessions there).
   - **Layout:** `agent/tasks/` always; `agent/notes/`, `agent/scripts/` and an untracked `tools/` for installed runtimes only where there's a use for them now, and anything to move. Moving or renaming a checkout, or turning extra clones into worktrees, is listed as its own item: it needs the user's explicit OK.
   - **`CLAUDE.md`:** an outline, the template's sections filled with what was found (layout, build and test commands, differences from the handbook such as the default branch). Leave out sections with nothing to say.
   - **`PLAN.md`:** the goal if there is one, the first task under "Now", the next few under "Then", a structure for the rest if the project has one (a cleanup flow, releases), and anything waiting.
   - **Task files:** one for the first task, with its status block; the others get theirs when they need detail.
   - **Version control** for these files: committed in the repository (public: nothing identifying in them, or `PLAN.md` and `agent/` in `.gitignore`), or the workspace folder as a private repository with the handbook's `.gitignore`, local only: no remote (it's for undoing a bad edit; the machine's backup covers the disk).
   - **Design documents:** where each part goes, per the handbook's "Design Documents and Roadmaps" (architecture, ADRs, milestones and issues, the plan), and when: linked from `CLAUDE.md` while it is still changing, or moved into the repository as the first task in `PLAN.md`.
   - **Anything existing** that would change: an existing `CLAUDE.md` or `AGENTS.md` is extended, not replaced.
5. **Stop** and ask the user to approve or discuss. Don't start Phase 2 on your own.

## Phase 2: Set Up

Only after the user approves, and as approved:

1. Create the workspace folder and move the checkout into it, if approved. Moving the folder the session runs in breaks the session: in that case, do the other steps first, give the user the exact commands (`mkdir`, `mv`) to run themselves, and tell them to start the next session in the new root.
2. Create the approved folders, and `CLAUDE.md`, `PLAN.md` and the first task file from the templates, filled in: no leftover placeholders or template comments. Write in the handbook's documentation style.
3. Set up version control for them as approved. For a workspace folder: `git init`, the handbook's `.gitignore` (adjusted for the approved folders), and check with `git status --short` that only the workspace's own files are tracked. Leave the files uncommitted for the user to review, per the handbook. Create no GitHub repository and push nothing unless the user asked for it.
4. Report what was created, folder by folder, then tell the user to review it, and to start the next session with `/next` (or "let's continue with PLAN.md").

## Updating a Workspace

Bring an existing workspace up to the current layout. Same two phases: **propose, then stop**; change only after the user approves.

1. **Read** `~/development/dev-handbook/CHANGES.md`, then the workspace's `CLAUDE.md`, `PLAN.md` (and `NEXT.md` if there is one), its `.gitignore`, and a listing of its own folders and scripts.
2. **Check each change** in `CHANGES.md` with its "Has it" test, newest first, until one passes. The ones above it are pending. If none are pending, say so and stop.
3. **Propose** the pending updates, oldest first, in the conversation, made concrete for this workspace, following each change's "Update" steps: what moves where; the task files to create (one line per task, and what goes in each); the new `PLAN.md` outline; notes that absorb non-task sections of the old plan; scripts whose paths need checking, and why; the new `.gitignore`; the `CLAUDE.md` lines that change; memory entries that mention the old layout. Ask about anything unclear (e.g. whether a long note belongs to a task). **Stop** for approval.
4. **Apply** as approved: `git mv` in a workspace repository, so history follows the files. Then run the change's checks: `git status --short`, every relative link resolves, the scripts' paths.
5. **Report** folder by folder, leave it uncommitted for the user's review, and name how to start the next session (`/next`, or "let's continue with PLAN.md").

## Rules

- Follow the handbook's guidelines and the user's standing instructions; this skill adds to them.
- Never delete anything. Never move, rename or re-clone a checkout without the user's explicit OK for that item.
- Keep identifying details (real addresses, hostnames, account names, personal paths) out of anything that will be committed to a public repository.
