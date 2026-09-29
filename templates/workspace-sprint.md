---
task: <slug>
phase: Research
status: active
branch: <branch>
repos: [<repository>, <another repository>]
---

# <Task Name>: <This Round>

<!--
Save as agent/sprints/<yyyy-mm-dd-hhmm>-<slug>.md in the workspace, for one
round of work on a task (usually one branch, from creation to merge) that
needs its evidence kept. Add to each section at the phase's handoff; don't
rewrite earlier ones. Read on demand, never at session start: the task file's
status block links here. What the task needs (decisions, new steps, bugs) goes
up into the task file or PLAN.md. At the round's end set status: done.
See guidelines/agent-workflow.md, "Sprint Notes".
-->

<What this round does, in a sentence or two. Task: [<Task Name>](../tasks/<slug>.md).>

## Research

- **Findings:** <what is true, each with its evidence: `file:line`, a command and its (trimmed) output.>
- **Reproduce:** `<command>` → <what it shows>
- **Rejected:** <an approach considered, and why not.>
- **Approach:** <the chosen approach.>
- **Assumptions:** <what the approach relies on being true; recheck these first if it fails.>

## Perform

- <What was changed, where. Failed attempts and why they failed.>

## Verify

- <What was run (`<command>`), and the result.>

## Finalize

- <The commits and merges, repository by repository.>
