---
name: allthethings
description: End a session on reviewed work - commit, merge --no-ff into the default branch, push, update the plan, commit the workspace repo, and name the next task. Only when the user types /allthethings.
disable-model-invocation: true
---

# All the Things

The user has reviewed the uncommitted changes and approves the full checkpoint. **This is explicit approval to commit, merge into the default branch, and push to the remote.** Do not ask again before any of these steps.

It ends the session: afterwards nothing exists only in the conversation, and the user can `/clear`.

Follow the project's own rules where they differ from these (its `CLAUDE.md`, `AGENTS.md`, `docs/development.md`, and `~/development/dev-handbook/guidelines/git-workflow.md` and `agent-workflow.md`), especially for the default branch name (`main` or `master`).

## Steps

Do these for every repo with changes from this session. A change can span several repos; handle each one, and name the repo whenever you report on it.

1. **Commit** on the work branch (never directly on the default branch; create the branch first if needed). Match the repo's commit style. Split into several commits when the changes are separate (e.g. generated copies vs. docs). End each message with the attribution lines from the system reminder, if there are any.
2. **Review** the branch before merging: if the workspace has `tools/scripts/review-branch.sh`, run `tools/scripts/review-branch.sh <repo> <branch> <default-branch>` from the workspace root. Otherwise check that the default branch is up to date with its remote, and scan the added lines for identifying details (real addresses, hostnames, account names, personal paths). **If anything is flagged, stop and ask**, rather than merging.
3. **Merge** into the default branch with `git merge --no-ff <branch>`, then delete the work branch (`git branch -d`).
4. **Push** the default branch to its remote (`git push origin <default-branch>`) **as a command of its own**, not chained with the merge, so a blocked push leaves the local steps done. If the push is refused (by a permission check, the remote, or a hook), don't try to get around it: give the user the exact command to run with `!`.
5. **Record** the checkpoint (per the agent workflow) in the workspace's plan file (`PLAN.md`): remove the finished task (its record is the commits and changelogs; at most a short "done" line with the merge commit if the plan keeps one), rewrite "Now" to the next task, and update any status notes the task touched.
6. **Save the rest.** Check the conversation for anything learned but not yet written down: workspace gotchas and new scripts go in the agent instructions (`CLAUDE.md`), bugs found but not fixed go in the plan (or the repo's Known Issues), preferences go in the relevant dev-handbook guideline. Move anything worth keeping out of the scratch folder.
7. **Commit the workspace repo**, if the workspace folder is a repository of its own (its `PLAN.md`, `CLAUDE.md`, notes and scripts; see the agent workflow's "Setting Up a Workspace"): commit its changes directly on its default branch with a short subject naming the task. It is local only by default: push it only if it has a remote and the workspace's instructions say to. If the workspace is the repository itself, the plan changes go in with steps 1-4 instead.
8. **Report** repo by repo: the commits (short hash and subject), the merge, and whether the push succeeded, and the workspace commit. Then:
   - say that everything is saved and `/clear` is safe;
   - name the task the next session would take from the plan (the "Now" task, or the top of "Next") in a line or two, so the user can reorder the plan before starting it;
   - tell the user to start the next session with "let's continue with PLAN.md".
