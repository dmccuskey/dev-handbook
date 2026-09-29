---
status: queued
phase: Start
repos: [<repository>]
branch: <branch>
---

# <Task Name>

<!--
Save as agent/tasks/<slug>.md in the workspace, and link it from PLAN.md. It
says what we're doing; the evidence from each round of work goes in a sprint
note in agent/sprints/. Keep it to what a session needs to resume: it is
read at the start of every session while the task is "Now".
status: queued, active, waiting or done. phase: Start, Research, Perform,
Verify or Finalize. Add fields as needed (e.g. release: 1.1).
See guidelines/agent-workflow.md, "Task Files" and "The Status Block".
-->

<What the task is and why, a few lines; link the GitHub issue if there is one.>

## Status

- Done: <what is finished so far, and whether it is uncommitted or committed>
- Left: <what remains>
- Decisions: <choices made along the way that the rest depends on>
- Next step: <the first thing the next session does>
- Sprint: [<file>](../sprints/<file>.md) <!-- the current round's sprint note, if it has one -->

<!-- Add sections as needed, e.g.: -->

## Steps

- [ ] <A step; a step that needs more than a few lines of its own becomes its own task, linked here.>
