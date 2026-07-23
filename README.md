# RepoResident

[![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
![Runtime: none](https://img.shields.io/badge/runtime-none-success.svg)
![Format: Markdown](https://img.shields.io/badge/format-Markdown-000000.svg)

**Make any coding agent think like it has lived in your codebase.**

RepoResident is a Git-native harness that turns a general coding agent into a project-aware
maintainer. It gives every session the project's goals, current state, architecture map,
decisions, and engineering workflows before work begins.

The agent reasons from the project, not only from the latest prompt.

No server. No database. No MCP dependency. No vendor lock-in. RepoResident is a small collection
of Markdown files that lives beside the code, travels with Git, and remains readable to both
people and agents.

## Why RepoResident

Coding agents are capable engineers, but each new session usually begins as an outsider. The
agent may understand the language and framework while knowing nothing about the project's
direction, current work, prior decisions, or quality expectations.

RepoResident supplies that missing operating context.

| Without RepoResident | With RepoResident |
|---|---|
| Responds to an isolated prompt | Reasons from project goals and live state |
| Re-explores the repository | Starts from a maintained architecture map |
| Improvises how to approach each task | Follows a workflow matched to the task |
| Reconsiders settled choices | Builds on recorded decisions and constraints |
| Optimizes for a quick completion | Designs for correctness and maintainability |
| Leaves useful context in chat history | Records useful context beside the code |

The result feels less like prompting a temporary assistant and more like assigning work to the
maintainer of the codebase.

## Results in practice

RepoResident was shaped through continued use on real project work. The most visible effects have
been:

- **More than 50% lower token usage in observed project sessions.** Directed navigation and
  bounded context reduce repeated repository exploration. This is an observed result, not a
  universal benchmark. Results depend on the model, repository, and task.
- **Higher prompt-cache reuse.** Stable operating instructions and repeatable workflows keep a
  large portion of each session consistent.
- **Stronger technical designs.** The agent designs with the project's goals, architecture,
  constraints, prior decisions, and known issues available.
- **Less shortcut-driven implementation.** Workflows explicitly reject stubs, silent scope
  reduction, and avoiding necessary complexity.
- **More maintainable code.** The operating manual prioritizes readability, obvious control flow,
  boundary handling, tests, and consistency with surrounding code.

RepoResident does not modify the model. It gives the model a better operating environment.

## Quick start

### Start a new project

1. Select **Use this template** on GitHub.
2. Create your new repository.
3. Open it with your coding agent.
4. Provide a short project brief:

```text
Bootstrap this project.

Goal: <what the project should achieve>
Stack: <languages and frameworks>
Constraints: <important technical or product requirements>
```

The bootstrap workflow verifies the project setup, records the initial architecture and commands,
and prepares the repository for normal work.

For a new project, bootstrap replaces this template README with the project's own README. The
RepoResident manual remains available in `.agent/README.md`.

### Adopt an existing project

Copy these items into the repository root:

```text
CLAUDE.md
AGENTS.md
.agent/
```

Then ask the coding agent:

```text
Bootstrap: adopt this repository.
```

The agent inspects the existing project, verifies its commands, builds the initial map, and records
only facts supported by the repository.

### Daily use

No special commands are required. Ask for work normally:

| Request | RepoResident behavior |
|---|---|
| "Add rate limiting to the API" | Designs the feature, requests approval, builds, verifies, and reviews |
| "Fix the crash on an empty upload" | Routes to a focused patch or evidence-first debugging workflow |
| "Refactor the payment service" | Establishes safety tests, plans mechanical steps, and preserves behavior |
| "Review this branch" | Reports verified, ranked findings without rewriting unrequested code |
| "Where were we?" | Reads current state and open issues without changing the repository |
| "Run maintenance" | Checks documentation budgets, drift, scratch files, and deferred issues |

## How it works

```text
request
  -> task routing
  -> current project state
  -> targeted project knowledge
  -> task-specific workflow
  -> verification and review
  -> durable project record
```

### Project awareness

`STATE.md` tells the agent what is active, what happens next, and what must not be forgotten.
`PROJECT.md` records architecture, constraints, domain terms, and cross-cutting risks.
`DECISIONS.md` prevents settled choices from being rediscovered or contradicted without cause.

### Directed navigation

`MAP.md` points the agent to the relevant modules before it searches the source. Area documents
hold deeper knowledge only where a module genuinely needs it. The agent reads the smallest useful
working set instead of surveying the whole repository.

### Workflow discipline

Each task type has its own procedure:

- Features require a complete design, approval, implementation, verification, and review.
- Known fixes stay small and focused.
- Unknown defects require reproduction and evidence before a fix.
- Refactors preserve behavior and proceed through verifiable mechanical steps.
- Reviews report confirmed findings with concrete failure scenarios.

This removes repeated process decisions from the prompt and gives the model a clear definition of
complete work.

### Bounded context

Project knowledge is split into layers. Every file has a specific role and most hot files have a
hard size limit.

| Layer | Content | Loaded |
|---|---|---|
| L0 | `CLAUDE.md`: operating rules and routing | Every session |
| L1 | `STATE.md`: current project state | Every session |
| L2 | One workflow for the current task | Per task |
| L3 | Map, project facts, decisions, issues, and area docs | Only when relevant |
| L4 | Source code | Targeted reads |

Old session history stays in an append-only journal and is searched only when needed. Project age
does not automatically increase the context loaded for a small task.

### Quality contracts

RepoResident makes engineering expectations explicit:

- No stubs, placeholder implementations, or silently reduced scope.
- Necessary complexity is designed and justified instead of avoided.
- Behavior changes include tests that would fail without the change.
- Boundaries account for empty input, invalid input, dependency failures, and timeouts.
- Code favors readability, maintainability, and consistency with local patterns.
- Completed work leaves verification evidence and an inspectable file trail.

## Repository layout

```text
CLAUDE.md
AGENTS.md
.agent/
  README.md
  STATE.md
  MAP.md
  PROJECT.md
  DECISIONS.md
  ISSUES.md
  workflows/
  designs/
  areas/
  journal/
  scratch/
```

| Path | Purpose |
|---|---|
| `CLAUDE.md` | Operating rules, task routing, quality standard, and project facts |
| `AGENTS.md` | Entry point for tools that load the `AGENTS.md` convention |
| `.agent/STATE.md` | Current focus, active work, next action, and watch-outs |
| `.agent/MAP.md` | Compact directory and module map |
| `.agent/PROJECT.md` | Architecture, constraints, glossary, and project-wide risks |
| `.agent/DECISIONS.md` | Binding technical decisions and their reasons |
| `.agent/ISSUES.md` | Bounded repository-local backlog |
| `.agent/workflows/` | Procedures for each category of engineering work |
| `.agent/designs/` | Active feature designs and archived design history |
| `.agent/areas/` | Optional deep documentation for complex modules |
| `.agent/journal/` | Append-only session outcomes and observations |
| `.agent/scratch/` | Ignored temporary notes for active investigations |

The full human manual is available at [.agent/README.md](.agent/README.md).

## Agent compatibility

RepoResident uses the instruction files already recognized by common coding tools.

| Tool | Entry point |
|---|---|
| Claude Code | `CLAUDE.md` |
| Codex | `AGENTS.md` |
| Cursor | `AGENTS.md` or `CLAUDE.md` |
| Windsurf | `AGENTS.md` |
| Other tools | Ask the agent to read `CLAUDE.md` before starting |

The harness itself is model-independent. A tool must be able to read repository files and follow
the operating manual for the full workflow to apply.

## Team mode

The default branch assumes one writer at a time. The `multi-team` branch adds an advisory claim
board, a branch integration workflow, and merge rules for shared harness files.

Team mode is optional. Solo projects keep the smaller default setup.

## Limitations

- RepoResident is an instruction and documentation system, not a security boundary.
- Workflow rules are visible and auditable, but the coding agent can still make mistakes.
- Project knowledge remains useful only when sessions maintain it as required.
- Quantitative improvements vary across repositories, models, and task types.
- The default setup is designed for one active writer. Use team mode for parallel branches.

## Contributing

Bug reports, workflow improvements, compatibility findings, and documentation corrections are
welcome. See [CONTRIBUTING.md](CONTRIBUTING.md) before opening a pull request.

## License

RepoResident is available under the [MIT License](LICENSE).
