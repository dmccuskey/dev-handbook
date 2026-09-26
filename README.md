# Dev Handbook

Setup guidelines for my project repositories: how they are organized, documented, and developed, with templates and instructions for coding agents.

It is written for people first. Agents read the same files through [AGENTS.md](AGENTS.md), a short index that says which guideline to read for which task.

## Contents

- [Documentation Guidelines](guidelines/documentation.md): the README as a Quick Start, the documentation home, kinds of pages, decision records, and writing style
- [Git Workflow](guidelines/git-workflow.md): a branch per change, commit messages, planned work, and public repositories
- [Templates](templates/): starting points for a [project README](templates/project-README.md), a [documentation home](templates/docs-README.md), and an [ADR](templates/adr.md)
- [AGENTS.md](AGENTS.md): the index for coding agents

## Setup

Clone this repository once, next to your projects:

```bash
git clone https://github.com/dmccuskey/dev-handbook.git ~/development/dev-handbook
```

Then point each agent harness at `AGENTS.md`, so the content lives in one place.

**Claude Code** reads `CLAUDE.md` and supports imports. Add this line to `~/.claude/CLAUDE.md`:

```markdown
@~/development/dev-handbook/AGENTS.md
```

**Codex** reads `~/.codex/AGENTS.md`:

```bash
ln -s ~/development/dev-handbook/AGENTS.md ~/.codex/AGENTS.md
```

**Other harnesses:** use the harness's global instructions file, and either link it to `AGENTS.md` or tell it to read that file.

Run `git pull` here to update. Every harness sees the change in its next session.

## Where Instructions Go

| Scope | Where |
|---|---|
| All projects | this repository |
| A group of related repositories | an `AGENTS.md` or `CLAUDE.md` in the folder that holds them (Claude Code reads these from parent folders) |
| One project | that project's `docs/development.md`, and its own `AGENTS.md` or `CLAUDE.md` |

Keep `AGENTS.md` short. An import loads the whole file into every session, so it lists guidelines to read when needed instead of importing them.
