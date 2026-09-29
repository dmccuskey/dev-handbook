---
name: next
description: Start or resume a session in an agent workspace - read PLAN.md's Now and Then and the task file, get each repo onto the task's branch, and carry on at the phase and Next step the task's status block names. Use when the user types /next or says "let's continue with PLAN.md" (or "with NEXT.md").
argument-hint: "[task slug, to take a task other than Now]"
---

# Next

Start the session on the workspace's current task, per the Start phase of `~/development/dev-handbook/guidelines/agent-workflow.md` ("Normal Flow"). Read as little as possible: the point is a cheap start after `/clear`.

If the user gave an argument, it names the task to take instead of "Now" (a task file's slug, or words from its line in `PLAN.md`).

## Steps

1. **Read the plan's top.** The agent instructions (`CLAUDE.md` or `AGENTS.md`) are already loaded. Read `PLAN.md` from the top through the end of "Then" only (`awk '/^## /&&t{exit} /^## Then/{t=1} 1' PLAN.md`). The rest of `PLAN.md` is for planning, not for starting.
   - **An older workspace with `NEXT.md`** keeps "Now", "Then" and the status block there: read `NEXT.md` instead, and follow the same steps with its status block.
   - **No "Now"** (the last task was finished and nothing moved up): take the top of "Then", and say so.
2. **Read the task file** it links (in `agent/tasks/`): the frontmatter and `## Status` always, the rest of the file when the phase or the Next step needs it (e.g. a design section it names). Read the sprint note only if Next step points into it, or the phase is Research or Verify and the task has one in progress.
3. **Check the repositories** named in `repos:`, each one: its current branch (`git branch --show-current`) and whether its working tree is clean.
   - **A branch is named** and exists: if the repo is on it, fine; if it's on another branch with a clean tree, check the named branch out.
   - **No branch yet** and the phase is Start: create one per the git workflow (named for the change, from an up-to-date default branch; the workspace's instructions name the default branch), and write it to the task's frontmatter. A task that changes nothing in a repository (planning, a workspace-only change) needs no branch.
   - **Anything unexpected** (uncommitted changes on the wrong branch, a named branch that's missing, a default branch behind its remote with local commits): report it and change nothing in that repo.
4. **Say where things stand**, in two or three lines: the task, its phase, the branch per repo, and the Next step. A phase of Start moves to the next phase the task needs; update the frontmatter when it does.
5. **Carry on with the Next step.** Read the workspace notes (`agent/notes/`) that the task or the agent instructions point to only when the step needs them. If the step needs the user (to propose a design, to review, a decision listed as waiting), do that part and stop for them.

## Rules

- Follow the agent workflow and the workspace's instructions; this skill adds to them. Where the workspace's instructions differ (a default branch of `master`, an order of work), they win.
- Never discard or stash uncommitted changes to switch branches.
- Keep the status block current as the session goes: update it at the end of each phase, per the agent workflow.
