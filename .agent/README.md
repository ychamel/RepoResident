# The Harness — manual for humans

A file-based operating system for AI agents working on this repo. Any session, on any model, in
any tool that reads `CLAUDE.md`/`AGENTS.md` (Claude Code, Cursor, Codex, …) gets: current state,
a procedure for its task type, and exactly as much project knowledge as that task needs.

## Design principles
1. **Context cost is O(task), not O(project age).** Hot state is size-capped and rewritten, not
   appended. History (journal, archived decisions, shipped designs) is append-only cold storage:
   grepped when investigating, never loaded by default. A 2-year-old repo costs a patch session
   the same tokens as a 2-week-old one.
2. **Knowledge is layered; sessions descend only while needed.**
   L0 `CLAUDE.md` (auto-loaded, ~85 lines) → L1 `STATE.md` (≤40) → L2 one workflow file (~40–60)
   → L3 MAP / PROJECT / area docs / active design (on demand) → L4 code (targeted reads only).
   Fixed overhead per session ≈ 2k tokens; everything beyond that is pulled by the task.
3. **Weak-model-proof.** Numbered steps, explicit gates and exit tests, fill-in templates,
   hard caps, verbatim checklists, and anti-guessing rules ("never call an API you haven't seen
   defined"). Procedure carries the intelligence so judgment is only needed where it's genuine.
4. **Completeness is contractual.** The design template's edge-case table must be filled and each
   row later ticked with evidence. Shortcuts (stubs, "for now", silently narrowed scope) are
   banned by the prime directives, and the Reviewer phase hunts for them.
5. **Self-maintaining.** Every harness file declares its own line cap; `workflows/maintain.md`
   runs every ~10 sessions to compact, verify docs against code, and triage accumulated flags.

## Personas = rule envelopes, not roleplay
- **Architect** — scopes and designs; forbidden to write code. Output: a design doc.
- **Builder** — implements the design slice by slice; forbidden to redesign.
- **Fixer** — minimal-diff patches; forbidden to refactor in passing (files a `flag:` instead).
- **Reviewer** — hostile fresh eyes on a diff; findings, not rewrites.
- **Curator** — maintains the harness itself (maintain.md).

Every handover between personas is an **artifact** (design doc → Builder, diff → Reviewer,
STATE.md → next session). That means phases can run as separate sessions — and reviews are
genuinely better run in a fresh session that didn't write the code. Between sessions, the
handover is the STATE.md rewrite: what's active, exact next step, watch-outs.

## Using it
- **New project:** copy this template, open a session: `Bootstrap this: <one-paragraph brief>`.
- **Existing repo:** copy `CLAUDE.md`, `AGENTS.md`, `.agent/` in, then: `Bootstrap: adopt this repo.`
- **Daily:** just ask for things; the routing table dispatches. Small asks stay cheap (a patch
  reads ~5 files); big asks produce a design before code.
- **Model mixing:** put the strongest model on designs and reviews; build slices, patches, and
  maintenance run fine on cheaper models — the design doc plus checklists carry the intent.
- **Multi-session features:** the design's work plan is sliced to ≤1 session each; STATE points
  at the next slice, so any session can resume without replaying history.
- **Parallel sessions:** the harness assumes one writer at a time. If you must parallelize,
  split by area and let only one session own STATE.md.
- **Trust the budgets.** If a harness file feels bloated or stale, say "run maintenance".

## File map
| File | Role | Loaded |
|---|---|---|
| `CLAUDE.md` | L0: directives, protocol, routing, project facts | every session (auto) |
| `AGENTS.md` | shim pointing non-Claude tools at CLAUDE.md | by other tools |
| `.agent/STATE.md` | hot state + inter-session handover | every session |
| `.agent/MAP.md` | one line per module — where things live | when locating code |
| `.agent/PROJECT.md` | architecture, constraints, glossary, landmines | feature work / confusion |
| `.agent/DECISIONS.md` | binding one-line decisions + archive | before designing |
| `.agent/workflows/*.md` | procedures: feature, patch, debug, refactor, review, maintain, bootstrap | one per task |
| `.agent/designs/` | one doc per feature (TEMPLATE.md); archive/ for shipped | active design only |
| `.agent/areas/` | deep docs for gnarly modules, created on demand | when working that area |
| `.agent/journal/` | append-only session log, monthly files | write-only; grep to investigate |
| `.agent/scratch/` | session working notes, gitignored | never by default |
