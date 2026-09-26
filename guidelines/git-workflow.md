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
2. Commit on the branch as often as useful. Small commits are preferred.
3. Before merging, run the tests, plus any other checks the project's `docs/development.md` requires for this kind of change. Documentation-only changes need no tests.
4. Merge into `main` with a GitHub pull request or `git merge`, push, and delete the branch.
5. If something runs from `main`, update it.

A branch holds one change. Unrelated changes go on separate branches, even when they are small, so each can be tested, merged, and reverted on its own.

### Pull Request or Direct Merge

Process is worth its cost only where it adds something. Use a pull request when the change deserves a look before it lands: code, documentation, decisions, anything a reviewer could question. Merge directly with `git merge` when the change is mechanical and was already checked where it came from, such as a rebuild that only brings in generated or vendored copies of code reviewed in its own repository. Tests and pre-merge checks still apply either way, and so does asking before a push.

The same goes for issues: open one for work that needs tracking beyond the current session, not for every small step.

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

## Planned Work

Decided work is tracked as labeled GitHub issues. Ideas that are not decided go under "Possible Future Changes" in the project's `docs/development.md` (see [Documentation Guidelines](documentation.md#planned-work-and-ideas)). A branch that finishes an issue references it in the pull request or commit (`Closes #12`).

## Public Repositories

Nothing identifying goes into a commit: no real email addresses, hostnames, account names, passwords, or personal paths, in code, examples, sample output, or test fixtures. Local configuration stays in gitignored files (such as `*.local.*`), with a committed `*.example.*` copy showing its shape. Check any recorded or captured data before committing it.
