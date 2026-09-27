---
name: allthethings
description: Checkpoint reviewed work - commit, merge --no-ff into the default branch, and push, then update the plan. Only when the user types /allthethings.
disable-model-invocation: true
---

# All the Things

The user has reviewed the uncommitted changes and approves the full checkpoint. **This is explicit approval to commit, merge into the default branch, and push to the remote.** Do not ask again before any of these steps.

Follow the project's own rules where they differ from these (its `CLAUDE.md`, `AGENTS.md`, `docs/development.md`, and `~/development/dev-handbook/guidelines/git-workflow.md` and `agent-workflow.md`), especially for the default branch name (`main` or `master`).

## Steps

Do these for every repo with changes from this session. A change can span several repos; handle each one, and name the repo whenever you report on it.

1. **Commit** on the work branch (never directly on the default branch; create the branch first if needed). Match the repo's commit style. Split into several commits when the changes are separate (e.g. generated copies vs. docs). End each message with the attribution lines from the system reminder, if there are any.
2. **Review** the branch before merging: if the workspace has `tools/scripts/review-branch.sh`, run `tools/scripts/review-branch.sh <repo> <branch> <default-branch>` from the workspace root. Otherwise check that the default branch is up to date with its remote, and scan the added lines for identifying details (real addresses, hostnames, account names, personal paths). **If anything is flagged, stop and ask**, rather than merging.
3. **Merge** into the default branch with `git merge --no-ff <branch>`, then delete the work branch (`git branch -d`).
4. **Push** the default branch to its remote (`git push origin <default-branch>`) **as a command of its own**, not chained with the merge, so a blocked push leaves the local steps done. If the push is refused (by a permission check, the remote, or a hook), don't try to get around it: give the user the exact command to run with `!`.
5. **Record** the checkpoint (per the agent workflow): update `PLAN.md` (mark the task done with its merge commit, and move "Now" to the next task), and any status notes the task touched. Check the conversation for anything learned but not yet written down (workspace gotchas go in `CLAUDE.md`, bugs found but not fixed go in the plan).
6. **Report** repo by repo: the commits (short hash and subject), the merge, and whether the push succeeded. Then say that everything is saved and `/clear` is safe, and tell the user to start the next session with "let's continue with PLAN.md".
