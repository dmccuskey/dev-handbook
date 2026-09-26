# Documentation Guidelines

How documentation is organized in my open source projects. [mail-filter](https://github.com/dmccuskey/mail-filter) is the reference example. Not every project needs every piece; a small library may need only the README. Add a page when there is content for it, not to fill the structure.

Starting points for new files are in [templates/](../templates/): the [README](../templates/project-README.md), the [documentation home](../templates/docs-README.md), and an [ADR](../templates/adr.md).

## The Main Idea

The main README exists to **get a new person up and running quickly**. It is a landing page and a "Hello World", not the manual.

- It takes the shortest path that works, on the most common platform, with the simplest setup.
- Anything more advanced, more robust, or platform-specific goes in a larger, more specific doc, and the README links to it.
- Readers who want more follow the links. Readers who just want to try the project never have to read past the Quick Start.

"Here is how to get it working quickly; to take this further, see *topic*" is the pattern throughout.

## Layout

```text
README.md                 landing page and Quick Start
LICENSE
docs/
├── README.md             documentation home: every doc, grouped by audience
├── installation.md       the full setup, per platform
├── <reference pages>     configuration, rule reference, API, ...
├── operations.md         running it day to day
├── architecture.md       how it works inside
├── development.md        contributing, testing, branches, future ideas
└── decisions/            architecture decision records (ADRs)
    ├── 001-<slug>.md
    └── ...
vendor/README.md          bundled third-party code: source, version, license, how to update
```

GitHub shows `docs/README.md` when someone opens the `docs/` folder, so it works as the documentation home without any site generator.

## The Main README

In this order:

1. **Name and one-line pitch.** What it does and for whom, in a sentence.
2. **Short description**, and a small example that shows what using it looks like (a config snippet, a code call, a command).
3. **Features.** About 8 to 10 bullets that matter to a user, not implementation details.
4. **Quick Start.** See below.
5. **Documentation.** Links to the four or five docs a user needs next, then one line pointing to the documentation home for everything else.
6. **How It Compares** (optional). One or two honest sentences per alternative, saying who each one suits.
7. **Safety** (when the software can do damage, e.g. it touches real data). The one precaution everyone should take.
8. **License**, including the licenses of any bundled code.

Keep out of the README: project structure, architecture, development history, testing, and contributor workflow. They belong on the documentation home or in `development.md`.

### The Quick Start

- **State the time, then the goal**, in two short sentences: first how long it takes and on what platform, then what the reader will have at the end. "The following steps will get you up and running in about 15 minutes on a Mac or Linux. You will have one account filtering with one rule." Don't pack both into one sentence because it can become confusing to read.
- **State the prerequisites** in one line, with a command to check each (`python3 --version`).
- **Cover the main path only.** Pick the most common platform. Other platforms get one sentence pointing to the installation page.
- **Numbered steps**, each small enough to finish and check before the next. Each step ends with something the reader can see working: a command's output, a file created, a log line.
- **Safe by default.** If the software acts on real data, the first run is a dry run or equivalent, and the step that turns it off is explicit.
- **Show the expected output**, with realistic but generic values, and say what a common failure looks like and what it means.
- **Mention, don't teach.** When a step depends on something outside the project (app passwords, virtual environments, a scheduler), name it and list what to search for. Only explain it if the reader can't continue without it.
- **End each step with a "Going further" line** that links to the more robust or advanced way: `**Going further:** keep the password out of the file with [password_env](docs/configuration.md#password_env)`.
- **End with how to update.**
- **Don't assume what isn't there yet.** A reader may start from an empty folder: "copy these into the root of your project folder", not "next to `main.lua`" when `main.lua` is created in a later step.
- **Follow it as written before publishing:** from a clean start, copying the steps and code exactly, on the platform it names.
- **Remove hurdles in the code, not the docs.** If a step needs a long explanation (editing source to enable a dry run, installing a dependency that fails on common systems), that is often a sign to change the software. File an issue and write the Quick Start after it is fixed.

## The Documentation Home (`docs/README.md`)

It opens by sending newcomers to the Quick Start, then lists every doc with a one-line summary of what it covers. Docs are grouped by **who is reading and what they are trying to do**, not by file type:

| Group | Reader and task | Examples |
|---|---|---|
| **Start** | New user, getting it running | Quick Start (link into the README), installation |
| **Use** | User, configuring and running it | configuration, reference pages, operations |
| **Internals** | Curious user or contributor, understanding how it works | architecture, protocol or platform notes, decisions |
| **Contribute** | Contributor, changing it | development, link to GitHub issues |

These groups follow [Diátaxis](https://diataxis.fr/) (tutorials, how-to guides, reference, explanation) loosely. Use them as headings on this page; don't reorganize `docs/` into matching folders unless the project has far more docs than one index can hold. Flat files keep links stable.

The page ends with the **Project Structure** tree, with a short comment on each entry and the user-created or gitignored files marked.

## Kinds of Pages

- **Installation**: the full version of the Quick Start's setup, one section per platform (macOS, Linux and Raspberry Pi, Windows), including scheduling or service setup, optional extras such as virtual environments, and updating. It opens by pointing back to the Quick Start as the short path.
- **Reference** (configuration, rules, API, CLI): complete and exact. Open with a quick-reference table, then one section per item with a heading that can be linked to (`#password_env`). Say what is *not* implemented, so readers stop looking.
- **Operations**: how to run it day to day: running, dry runs, logs and the log format, scheduling, log rotation, credentials, recovery.
- **Architecture**: overview, structure, processing flow, safety model, design history, and a list of the ADRs.
- **Notes** on an external system the project depends on (IMAP, a provider, an API): the behavior that surprised us and how the code handles it.
- **Development**: philosophy, current baseline, possible future changes, testing, branch workflow, and historical context.

## Decision Records (`docs/decisions/`)

An ADR records an important technical decision about how the system works, and why, when the reason would not be obvious from the code. Not every decision needs one: smaller choices are explained in the commit message.

- **Name:** `NNN-short-slug.md`, numbered in order and never renumbered.
- **Title:** `# ADR NNN: <Decision>`.
- **Sections:** `**Status:**` (Proposed, Accepted, Superseded by ADR NNN), `## Context` (the problem and the constraints, with evidence such as real server output), `## Decision` (what we do, specifically enough to check the code against it), `## Consequences` (what follows, good and bad).
- **Don't rewrite history.** When a decision changes, add a dated amendment section and update the status line (`Accepted; amended 2026-09-24 (see [Amendment](#...))`), or write a new ADR that supersedes it.
- **Link them.** ADRs link to the ADRs they build on. The architecture page and other docs link to the relevant ADR rather than repeating its reasoning.
- **List them** in `architecture.md` (or a `decisions/README.md`), and keep the list current when adding one.

## Planned Work and Ideas

- **Decided work** goes in the workspace's `PLAN.md` while the project is under active work, and in detailed GitHub issues as it winds down (see [Planned Work](git-workflow.md#planned-work)). The docs link to the issues; they don't keep their own roadmap or TODO list.
- **Undecided ideas** go under "Possible Future Changes" in `development.md`, which says plainly that each needs discussion and a concrete use case before it is worked on.
- When an idea is decided, it moves out of the doc and into the plan or an issue.

## Writing Style

- **Plain, direct sentences.** Say what to do and what happens. Avoid "simply", "just", and "easy".
- **Title Case headings**, short enough to make readable anchors.
- **Copy-pasteable examples** that work as written. Show commands in fenced blocks with the language set.
- **Make example names teach.** When two values could be confused, make them visibly different (a folder called `My Filtered Folder` with the key `filtered_key`, not `Filtered` and `filtered`). Choose sample IDs that suggest naming styles a reader might use (`gmail-work`, `fastmail.com`), not the author's own.
- **No identifying details.** The repository is public: no real addresses, hostnames, account names, or personal paths in examples, sample output, or test fixtures.
- **Link, don't repeat.** Each fact lives in one place. Other pages link to its section.
- **Relative links** between docs, with section anchors, so they work on GitHub and in a clone.
- **Show real output** for logs and commands, trimmed and made generic.
- **Screenshots** show just the app: a tight crop, no device frame or window chrome. To show something that changes, put two side by side (e.g. running and finished). Check them for identifying details before committing. Example apps get one each, shown next to their description in the examples README (e.g. `examples/screenshots/<app>.png`), so a reader can see what each example does before opening it.
- **Performance numbers** are published as a baseline for a version, with the setup they were measured on, before improving them; improvements are then shown as before/after against that baseline.

## Keeping Docs Current

- A change that affects behavior updates the docs on the same branch.
- Documentation-only changes go on their own `docs/<name>` branch, with a `docs:` commit message.
- When adding a doc, add it to the documentation home and, if a user needs it, to the README's Documentation list.
- Bundled third-party code is described in `vendor/README.md`: source, version, license, and the steps to update it.
