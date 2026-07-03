# Agent Operating Manual

You operate this repo through the harness in `.agent/` — persistent state and procedures that let
any session, on any model, pick up work with minimal context and leave the repo better than found.
First session on this repo? `.agent/STATE.md` will route you to bootstrap.

## Prime directives
1. The user outranks this file; this file outranks habit. When rules conflict:
   Hard rules > active workflow > style sections.
2. Correct and complete beats fast. No stubs, no placeholder code, no mock data outside tests,
   no silently narrowed scope. If you can't finish honestly, say exactly what's missing.
3. Smallest sufficient context: `.agent/MAP.md` → area doc if one exists → grep → read only
   implicated files. Never read directories wholesale. Lost after ~3 file reads? Re-scope from
   MAP or the area doc instead of reading more code.
4. Never call an API/function you haven't seen defined this session — read its definition first.
   Unsure how something behaves? Verify in source, not from memory.
5. Code is truth; docs serve code. A wrong harness doc is a bug — fix it in passing (≤5 lines)
   or leave a `flag:` in the journal.
6. Chat carries outcomes; files carry detail. Long analysis → `.agent/scratch/`,
   designs → `.agent/designs/`, lasting choices → `.agent/DECISIONS.md`.

## Session protocol
START: read `.agent/STATE.md` → classify the request (routing below) → open that one workflow
file and follow it. Read only what the workflow tells you to read.
DURING: after each completed slice/checkpoint, update the `Active:` line in STATE.md (crash insurance).
END — mandatory whenever you changed anything:
1. Rewrite `.agent/STATE.md` in full (template is inside it; Session +1).
2. Append to `.agent/journal/<YYYY-MM>.md`: `S<n> <YYYY-MM-DD> <workflow>: outcome + key files`
   (≤4 lines; optionally `  flag: <debt/risk noticed>`).
3. Structure changed → update MAP.md. Lasting choice made → one line in DECISIONS.md.
4. Tick your workflow's Done checklist in your final message.
If the new Session number is a multiple of 10, add "maintenance due" to STATE `Next:`
(run `.agent/workflows/maintain.md` now if the session has room).

## Routing
| Request looks like | Route |
|---|---|
| New capability; or touches >2 files, or any interface/schema/dependency | `.agent/workflows/feature.md` |
| Small fix or tweak, cause known | `.agent/workflows/patch.md` |
| Defect, cause unknown | `.agent/workflows/debug.md` |
| Restructure with zero behavior change | `.agent/workflows/refactor.md` |
| Review a diff / PR | `.agent/workflows/review.md` |
| Harness upkeep / maintenance due | `.agent/workflows/maintain.md` |
| Question or read-only analysis | Answer from MAP/PROJECT.md + targeted reads. Change nothing. Skip session END. |

Each workflow states an exit test; when on the fence, start with the lighter workflow.

## Output contract
- Lead with the outcome. Progress notes ≤2 sentences; never narrate tool calls or echo file contents.
- ≤10 lines of code in chat unless asked; reference `path:line` instead.
- Task close: Did / Changed (files) / Verified (how, with actual output) / Next — ≤6 lines, plus the Done checklist.
- Ask the user only what only the user can answer; otherwise decide, record the decision, proceed.
- Report failures verbatim (real test output, real errors) — never summarized optimism.

## Code standard
- Write for the reader: descriptive names, small functions, early returns, obvious control flow.
  Spend complexity on the design (edge cases, failure modes) — never on clever code.
- Every boundary you touch handles: empty/null, invalid input, dependency failure/timeout.
  "Happy path only" is incomplete work.
- Every behavior change ships with a test that fails without it.
- Comments only for constraints and whys the code can't express. No dead or commented-out code.
- Match the file's existing local style when it conflicts with this section.

## Hard rules
- Never: commit secrets · force-push · delete/overwrite content you haven't read · edit journal
  history or archives.
- New dependency ⇒ a DECISIONS.md line justifying it.
- Debug instrumentation is removed before done (keep a ledger while it exists).
- Data-touching changes (migrations, deletions) need a written revert path in their design.

## Project facts  <!-- filled by bootstrap; keep ≤20 lines -->
- What: <fill: one sentence>
- Stack: <fill: languages, frameworks, versions>
- Commands — run: `<fill>` · test: `<fill>` · lint: `<fill>` · build: `<fill>`
- Entry points: <fill: main files>
- Deeper facts: `.agent/PROJECT.md` (architecture, constraints, glossary, landmines)
