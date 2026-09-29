# Plan

<!--
Save as PLAN.md at the workspace root, next to CLAUDE.md. It is the index of
the work: one line per task, linking its task file in agent/tasks/. Sessions
read "Now" and "Then" first; everything else here is read when planning.
Below "Then", use any structure that fits the project (a cleanup flow, fixes
in dependency order, releases). Finished tasks leave this file; their task
files stay, marked done. See guidelines/agent-workflow.md, "The Plan Files".
-->

Workspace plan for `<workspace>`, per the dev-handbook's agent workflow. `CLAUDE.md` has the layout, tools and gotchas.

<The goal of the current round of work, in a sentence or two, if there is one.>

## Now

- [<Task in progress>](agent/tasks/<slug>.md)

## Then

<!-- The next few tasks (about five), most important first. -->

- [<Next task>](agent/tasks/<slug>.md)

## <A Group, e.g. "Release 1" with "### 1.0", "### 1.1", or "Fixes, Bottom Up">

<A line or two on how this group works; link a note in agent/notes/ for its procedure.>

- [<Task>](agent/tasks/<slug>.md)
- <A tiny task, as a plain line until it is taken.>

## Waiting

- [<Task>](agent/tasks/<slug>.md): <blocked on whom or what>
