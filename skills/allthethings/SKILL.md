---
name: allthethings
description: End a session on reviewed work - commit, merge --no-ff into the default branch, push, update PLAN.md and the task file, commit the workspace repo, and name the next task. Only when the user types /allthethings.
disable-model-invocation: true
---

# All the Things

The user has reviewed the uncommitted changes and approves the full checkpoint. **This is explicit approval to commit, merge into the default branch, and push to the remote.** Do not ask again before any of these steps.

It ends the session: afterwards nothing exists only in the conversation, and the user can `/clear`.

Follow the project's own rules where they differ from these (its `CLAUDE.md`, `AGENTS.md`, `docs/development.md`, and `~/development/dev-handbook/guidelines/git-workflow.md` and `agent-workflow.md`), especially for the default branch name (`main` or `master`).

## Steps

Do these for every repo with changes from this session. A change can span several repos; handle each one, and name the repo whenever you report on it.

1. **Commit** on the work branch (never directly on the default branch; create the branch first if needed). Match the repo's commit style. Split into several commits when the changes are separate (e.g. generated copies vs. docs). End each message with the attribution lines from the system reminder, if there are any.
2. **Review** the branch before merging: if the workspace has `agent/scripts/review-branch.sh`, run `agent/scripts/review-branch.sh <repo> <branch> <default-branch>` from the workspace root. Otherwise check that the default branch is up to date with its remote, and scan the added lines for identifying details (real addresses, hostnames, account names, personal paths). **If anything is flagged, stop and ask**, rather than merging.
3. **Merge** into the default branch with `git merge --no-ff <branch>`, then delete the work branch (`git branch -d`).
4. **Push** the default branch to its remote (`git push origin <default-branch>`) **as a command of its own**, not chained with the merge, so a blocked push leaves the local steps done. If the push is refused (by a permission check, the remote, or a hook), don't try to get around it: give the user the exact command to run with `!`.
5. **Record** the checkpoint (per the agent workflow's "The Plan Files"):
   - **The sprint note** of this round (in `agent/sprints/`, if it has one): `status: done`, `phase: Finalize`.
   - **The task file** (in `agent/tasks/`): if the task is finished, `status: done` and a last status block (Done with the merge commits); it stays as the record. If it goes on, update its status block for the next round (phase, Done, Left, Next step) and check off the step.
   - **`PLAN.md`:** if the task is finished, remove its line, move the top of "Then" up to "Now" (set its task file to `status: active`, `phase: Start`), and refill "Then" to about five tasks from the sections below it. A task that isn't in "Then" yet may need a task file first. Update any group notes the task touched.
   - **An older workspace with `NEXT.md`** keeps "Now" and "Then" there, with the status block, and the backlog in `PLAN.md`: remove the finished task from `NEXT.md`, make the top of "Then" the new "Now", and refill "Then" from `PLAN.md`.
6. **Save the rest.** Check the conversation for anything learned but not yet written down: workspace gotchas and new scripts go in the agent instructions (`CLAUDE.md`), bugs found but not fixed go in the task file or `PLAN.md` (or the repo's Known Issues), preferences go in the relevant dev-handbook guideline. Move anything worth keeping out of the scratch folder.
7. **Commit the workspace repo**, if the workspace folder is a repository of its own (its `CLAUDE.md`, `PLAN.md` and `agent/` folder: tasks, sprint notes, notes and scripts; see the agent workflow's "Setting Up a Workspace"): commit its changes directly on its default branch with a short subject naming the task. It is local only by default: push it only if it has a remote and the workspace's instructions say to. If the workspace is the repository itself, the plan changes go in with steps 1-4 instead.
8. **Report** repo by repo: the commits (short hash and subject), the merge, and whether the push succeeded, and the workspace commit. Then:
   - say that everything is saved and `/clear` is safe;
   - name the task the next session would take (the new "Now" in `PLAN.md`) in a line or two, so the user can reorder the plan before starting it;
   - tell the user to start the next session with "let's continue with PLAN.md" (in an older workspace with `NEXT.md`: "let's continue with NEXT.md").
