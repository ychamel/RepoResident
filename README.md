# Agent Harness

**A file-based operating system for AI coding agents.** Copy one folder and two files into any
repository, and every agent session — on any model, in any tool that reads `CLAUDE.md` or
`AGENTS.md` (Claude Code, Cursor, Codex, Windsurf, …) — starts knowing the project's current
state, follows a written procedure for its task type, and leaves the repo better documented
than it found it.

No server, no database, no MCP, no vendor lock-in. The whole harness is ~20 small Markdown
files that live in git, merge like code, and are as readable to you as to the agent.

> This README describes the template itself. When you bootstrap a project from it, the agent
> replaces this file with your project's own README — the harness manual stays in
> [.agent/README.md](.agent/README.md).

## The problem it solves

AI coding sessions are amnesiac. In a repo of any real age, each session re-explores the
codebase (dozens of files, tens of thousands of tokens), re-derives decisions that were settled
months ago — sometimes contradicting them — and everything it learned dies when the context
window closes. Cost grows with project age. Quality drifts with model mood. Knowledge
evaporates nightly.

The harness fixes this with three moves:

1. **State lives in files, not in the chat.** A size-capped `STATE.md` carries "what's active,
   what's next, what to watch out for" between sessions. History is append-only and grepped,
   never loaded.
2. **Procedure carries the intelligence.** Every task type routes to a numbered workflow with
   explicit gates, templates, and exit checklists — so the *process* stays strong even when the
   model is small.
3. **The docs maintain themselves.** Every harness file declares its own line cap, and a
   maintenance workflow runs every ~10 sessions to compact state, verify docs against code, and
   triage accumulated debt. The harness cannot silently rot.

## What you get

### Token cost that stays flat as the project grows

Knowledge is layered; a session descends only as deep as its task needs:

| Layer | What | Size | Loaded |
|---|---|---|---|
| L0 | `CLAUDE.md` — operating manual | ~80 lines | every session (automatic) |
| L1 | `.agent/STATE.md` — what's happening now | ≤40 lines | every session |
| L2 | one workflow file for the task type | ~30–60 lines | one per task |
| L3 | MAP / PROJECT / area docs / active design | capped each | only when the task needs them |
| L4 | source code | — | targeted reads only |

Fixed overhead is roughly **2k tokens per session**; everything beyond that is pulled by the
task, not by the project's age. A patch in a two-year-old repo costs what it cost in week two,
because history (journal, closed issues, archived decisions) is cold storage: greppable,
never auto-loaded. Compare that to an agent "getting oriented" by reading 30 files.

### Code quality enforced by contract, not vibes

- **Routing:** features get a design before code; patches get minimal diffs; unknown-cause bugs
  get evidence-first debugging. The wrong shortcut is structurally unavailable.
- **The edge-case table is a completeness contract.** Every design enumerates empty/boundary/
  failure/concurrency cases; the builder must tick each row with the test that covers it.
- **No stubs, no "for now", no silently narrowed scope** — banned by prime directive, hunted by
  the review phase.
- **Personas as rule envelopes:** the Architect may not write code, the Builder may not
  redesign, the Fixer may not refactor in passing, the Reviewer reports findings instead of
  rewriting. Handovers are artifacts (design doc, diff, state file), so phases can run as
  separate sessions — reviews are genuinely better in a fresh session that didn't write the code.
- **Every behavior change ships with a test that fails without it.**

### Project knowledge with a permanent address

Everything a new contributor — human or agent — needs is findable in seconds, not archaeology:

- `STATE.md` — what's in flight right now (30-second catch-up)
- `MAP.md` — one line per module: where things live
- `PROJECT.md` — architecture, constraints, glossary, landmines
- `DECISIONS.md` — every binding choice, one line, with the why
- `ISSUES.md` — the tracked backlog (see below)
- `journal/` — append-only session history; `designs/` — why each feature is shaped as it is

Humans benefit as much as agents: `git log` tells you what changed, the harness tells you *why*
and *what's next*.

### A local issue tracker built for agents

`.agent/ISSUES.md` tracks bugs, debt, and deferred work as one line each — id, date, priority
(P1/P2/P3), scope, evidence — with hard caps and a closed-history section that rolls off into
git history. It scales because the hot set is bounded: agents scan ~40 lines, never a ticket
database. Casual observations enter as journal `flag:` lines and are *promoted* to issues at
maintenance; deferred review findings and unreproducible bugs are filed automatically by their
workflows; designs consult the backlog for the area they touch. Nothing relies on an external
tracker — the backlog travels with the repo, works offline, and merges in git.

### Weaker (cheaper) models stay on the rails

The harness assumes the model executing it might be small: numbered steps, explicit gates
("STOP and return to phase 2"), fill-in templates, hard line caps, verbatim done-checklists,
and anti-guessing rules ("never call an API you haven't seen defined this session"). Judgment
is only required where it's genuine. That makes model-mixing a cost lever:

| Work | Model tier |
|---|---|
| Designs, reviews, debugging unknowns | strongest you have |
| Building slices from an approved design | mid |
| Patches, maintenance, journal upkeep | cheap |

The design doc plus checklists carry the intent across the gap.

### Team mode (optional)

The `multi-team` branch adds multi-writer support: an advisory claim board (`BOARD.md`), an
integrate workflow (claim → sync → merge → release), and git merge rules that keep `STATE.md`
branch-local while union-merging journals, decisions, and issues. Solo work stays on `master`.

## Quick start

**New project** — use GitHub's *Use this template* button, or:

```bash
git clone <this-repo> my-project && cd my-project && rm -rf .git && git init
```

Open an agent session and say:

> Bootstrap this: <one-paragraph brief — what, stack, constraints>

**Existing repo** — copy `CLAUDE.md`, `AGENTS.md`, and `.agent/` in, then:

> Bootstrap: adopt this repo.

**Daily use** — just talk; the routing table dispatches:

| You say | What happens |
|---|---|
| "Add rate limiting to the API" | feature workflow: design → approval gate → build → review |
| "Fix the crash on empty upload" | patch (cause known) or debug (evidence-first) workflow |
| "File an issue: exports drop timezones" | one line in `ISSUES.md`, nothing else touched |
| "What's open?" / "Where were we?" | reads STATE + ISSUES, answers, changes nothing |
| "Run maintenance" | compaction sweep: budgets, doc-vs-code spot checks, flag triage |

## Layout

```
CLAUDE.md            # L0: the agent operating manual (auto-loaded)
AGENTS.md            # shim pointing non-Claude tools at CLAUDE.md
.agent/
  README.md          # the human manual — start here
  STATE.md           # hot state + inter-session handover (≤40 lines)
  MAP.md             # one line per module
  PROJECT.md         # architecture, constraints, glossary, landmines
  DECISIONS.md       # binding choices, one line each + archive
  ISSUES.md          # backlog: bugs/debt/deferred work + closed history
  workflows/         # feature · patch · debug · refactor · review · maintain · bootstrap
  designs/           # one design doc per feature (TEMPLATE.md) + archive/
  areas/             # deep docs for gnarly modules, created on demand
  journal/           # append-only session log, monthly files
  scratch/           # session working notes (gitignored)
```

## FAQ

**Isn't 2k tokens of overhead still overhead?** Yes — and it buys the session out of re-reading
the codebase to orient itself, which routinely costs 10–50× that. The overhead is fixed; the
savings compound with repo size and session count.

**Why files instead of a memory database or MCP server?** Files are diffable, reviewable in PRs,
merge with git, work in every agent tool ever made, survive tooling churn, and cost nothing to
host. Your project's memory shouldn't live in a vendor.

**Will the agent actually follow it?** The manual is ~80 lines and auto-loaded, workflows are
checklists with exit tests the agent must tick in its final message, and maintenance
spot-checks docs against code every ~10 sessions. It's not unbreakable — it's cheap to audit:
everything the agent should have done leaves a file trail.

**What if the docs drift from the code?** Prime directive: *code is truth; a wrong doc is a
bug* — fixed in passing or flagged. Maintenance probes random doc claims against the code and
fixes lies. Budgets force pruning, so stale lines can't hide in bulk.

**Which tools work?** Anything that auto-reads `CLAUDE.md` (Claude Code) or `AGENTS.md`
(Cursor, Codex, and most others). Worst case, paste one line: *"Read CLAUDE.md and follow it."*
