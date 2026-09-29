# <Workspace Name>

<!--
Save as CLAUDE.md (or AGENTS.md) at the workspace root: the repository root,
or the folder that holds several repositories. Lasting knowledge only; the
state of the work goes in PLAN.md and agent/tasks/. Keep it short: it is loaded into every
session, so it is an index. What every session needs stays here; detail needed
only for some tasks goes in agent/notes/, linked under "Notes". Leave out
sections with nothing to say yet.
See guidelines/agent-workflow.md, "Setting Up a Workspace".
-->

<What this workspace is, in one or two sentences.> What to work on: [PLAN.md](PLAN.md), per the dev-handbook's agent workflow.

## Layout

<!-- For a folder of repositories: one row per repository or group. -->

| folder | holds |
|---|---|
| `<repo>/` | <what it is> |

## Working Rules

The dev-handbook guidelines apply as written, except:

- <A difference, e.g. "The default branch is `master`, not `main`.">
- <Order of work, repositories that depend on each other, what to ask before doing.>

## Build and Test

- Build: `<command>`, from `<folder>`.
- Test: `<command>`. <What a passing run looks like.>
- Verify: <how the Verify phase checks a change here, e.g. `npm test` and the page in a browser.>

## Helper Scripts

<!-- Scripts kept in the workspace's agent/scripts/: name, what it does, when to use it.
     Once there are more than a few, list them in agent/scripts/README.md and link it under "Notes". -->

- `<script>`: <what it does>

## Gotchas

<!-- Quirks learned the hard way: the symptom, the cause, what to do. -->

- **<Short name>:** <symptom, cause, fix>

## Notes

<!-- Detail read on demand, one row per note in agent/notes/ (or agent/scripts/README.md). -->

| read | when |
|---|---|
| [agent/notes/<topic>.md](agent/notes/<topic>.md) | <the tasks that need it, e.g. "before changing the build"> |
