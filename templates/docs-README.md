# <Project Name> Documentation

<!-- Save as docs/README.md. GitHub shows it when someone opens docs/. Leave out groups with no pages yet. -->

New to <Project Name>? Start with the [Quick Start](../README.md#quick-start) in the main README.

## Start

- [Quick Start](../README.md#quick-start): <the result it gets you>
- [Installation](installation.md): <platforms, scheduling, updating>

## Use

- [Configuration](configuration.md): <what it covers>
- [<Reference page>](<page>.md): <what it covers>
- [Operations](operations.md): <running it day to day>

## Internals

- [Architecture](architecture.md): system structure, processing flow, and the safety model
- [Architecture Decisions](decisions/): the project's decision records (ADRs)

## Contribute

- [Development](development.md): testing, the branch workflow, and possible future changes
- Planned work is tracked in [GitHub issues](https://github.com/dmccuskey/<project>/issues)

## Project Structure

```text
<project>/
├── <main entry point>        # <what it is>
├── <config>.example.<ext>
├── <config>.local.<ext>      # you create (gitignored)
├── docs/
│   ├── README.md             # this page
│   └── decisions/            # ADRs
├── tests/
├── LICENSE
└── README.md
```
