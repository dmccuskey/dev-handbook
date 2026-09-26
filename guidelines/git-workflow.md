# Git Workflow

How changes are made, committed, and merged in my projects. A project's own `docs/development.md` adds what is specific to it, such as extra tests required before a merge or how it is deployed.

## The Main Idea

`main` holds only code that has passed testing. Something may be running from it: a deployment that runs `git pull` on `main`, or users who clone it and run it. Every change is therefore developed on its own short-lived branch and merged only once it is tested.

Short-lived feature branches are preferred over a long-lived `dev` branch, so that each change can be tested, merged, and reverted on its own.

## Branches

Name the branch for the change, with a prefix for its kind:

```text
feat/<name>    new behavior
fix/<name>     bug fixes
docs/<name>    documentation only
```

`test/`, `refactor/`, and `config/` are also fine for changes that are only that.

Workflow:

1. Branch from an up-to-date `main`: `git switch main && git pull && git switch -c feat/<name>`.
2. Make the change and run the tests, plus any other checks the project's `docs/development.md` requires for this kind of change. Documentation-only changes need no tests.
3. Leave the change **uncommitted** for me to review: uncommitted changes are easy to read in the editor's source-control view, committed ones are not. Commit once I've approved it; one commit per branch is normal.
4. Merge into `main` with `git merge --no-ff`, so the branch stays visible as one merge in the history, push, and delete the branch. Pushing always waits for my go-ahead.
5. If something runs from `main`, update it.

A branch holds one change. Unrelated changes go on separate branches, even when they are small, so each can be tested, merged, and reverted on its own.

### Pull Requests, Sparingly

I work alone, so process is worth its cost only where it adds something. The review happens before the commit (step 3), so most changes are merged directly. Open a pull request only for:

- a large or risky code change, worth reading as a whole on GitHub;
- a decision I want to think over before it lands;
- anything I ask to have as a pull request.

Tests and pre-merge checks apply either way, and so does asking before a push.

## Commit Messages

```text
<type>: <what the change does, lowercase, imperative>

Why the change was made and anything a reviewer would not see from the
diff: the problem, the approach, what was tested. Wrap at about 72
characters.
```

- `<type>` matches the branch prefix: `feat`, `fix`, `docs`, `test`, `refactor`, `config`.
- The subject says what the change does, not which files it touches: `docs: add a Quick Start, installation page, and docs home`.
- The body is optional for a change that explains itself.
- A branch that finishes a GitHub issue says so in the commit (`Closes #12`).

## Planned Work

While a project is under active work, decided work goes in the workspace's `PLAN.md` (see [Agent Workflow](agent-workflow.md#the-plan-file)): at the start there are many small tasks, and a plan file is much quicker to keep than issues.

As the work winds down and what's left is future work rather than next steps, move it out of the plan into GitHub issues, so it's still findable when the project is picked up again:

- **One issue per piece of work,** detailed enough to start from cold: `### Goal` (what it should do), `### Why` (the problem or use case), then the details, a checklist where it helps.
- **Labels** for the kind (`enhancement`, `documentation`, `testing`, an area of the project) and `priority: low|medium|high`.
- The project's docs link to the issues where relevant.

Also open an issue at any time for something others should see, such as a bug users may run into. Ideas that are not decided go under "Possible Future Changes" in the project's `docs/development.md` (see [Documentation Guidelines](documentation.md#planned-work-and-ideas)).

## Public Repositories

Nothing identifying goes into a commit: no real email addresses, hostnames, account names, passwords, or personal paths, in code, examples, sample output, or test fixtures. Local configuration stays in gitignored files (such as `*.local.*`), with a committed `*.example.*` copy showing its shape. Check any recorded or captured data before committing it.
