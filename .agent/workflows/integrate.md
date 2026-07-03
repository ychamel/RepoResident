# Workflow: Integrate — team-mode branch lifecycle: claim → sync → merge → release
For: starting parallel work in a shared repo, keeping a branch current with main, merging it back.
Exit test — solo repo (you are the only writer)? This workflow is inert; work on main as usual.

One-time per clone: `git config merge.ours.driver true` — activates the STATE.md rule in
`.gitattributes`. Without it STATE.md conflicts on merge; resolve by keeping main's side.

## A · Claim — before the first line of code (you are on main)
1. Pull main. Open `.agent/BOARD.md`.
2. Do your target areas (MAP.md paths) overlap an existing row? STOP — surface it to the user:
   coordinate with that owner, narrow your claim, or queue behind their merge.
   Never silently double-claim; never edit or work inside someone else's claimed areas.
3. Add your row (branch, owner, goal, claimed areas, date). Commit to main and push:
   `board: claim <branch>`. This tiny commit is the lock.
4. Pull once more and re-check the board — a racing claim may have landed; on overlap, go to 2.
5. Create your branch. Work there under the normal routed workflows (feature/patch/debug/…).
   Need an area you didn't claim? Return to main and widen your claim via steps 1–4 first.

## B · Sync — merge main into your branch; do it before C, and whenever main moves under you
1. `git merge main` in your branch (merge, not rebase — pushed history stays stable).
2. Harness files resolve by the rules table below. Code conflicts: resolve here, in your
   branch, never during the merge to main.
3. Tests green before proceeding — report real output.

## C · Merge to main
1. GATE — all true, else stop and finish the missing piece:
   branch green (tests/lint/build) · branch journal/design updated per its workflow ·
   B ran after main's latest commit, so this merge is conflict-free.
2. `git merge --no-ff <branch>` into main (`--no-ff`: one revert point per integration).
   Harness merge rules (mostly automatic via `.gitattributes`):
   | File | Rule |
   |---|---|
   | `STATE.md` | branch-local; whichever side git keeps is discarded — D2 rewrites it before this session ends |
   | `journal/*.md`, `BOARD.md` | union — both sides' lines kept |
   | `DECISIONS.md` | union; two branches minted the same `D<n>`? renumber yours to the next free number and fix references |
   | `MAP.md`, `PROJECT.md`, `areas/` | normal merge; on conflict keep both sides' knowledge, dedupe by hand |
   | `designs/` | one file per feature — no conflicts; keep both |
3. Full test suite on merged main. Red → fix now or revert the merge. Main is never left red.

## D · Release
1. Delete your BOARD.md row; append one line to its Integration log.
2. Rewrite main's `STATE.md` (Session +1 on main's own counter): Focus and Recently shipped
   reflect the merge; carry over any still-true watch-outs from the branch.
3. Journal line on main: `S<n>/<owner> <YYYY-MM-DD> integrate: <branch> merged — outcome + key files`.
4. Integration log grown to ~10 lines since the last maintenance? Add "maintenance due" to
   STATE `Next:` (maintain.md runs on main only — never on a feature branch).
5. Push main; delete the merged branch.

## Done — copy into your final message and tick honestly
- [ ] Claim row existed before work started; deleted at release
- [ ] Sync (B) ran last; all conflicts resolved in-branch, none on main
- [ ] Merged main fully green (output shown); harness files match the rules table
- [ ] Integration log line added; main STATE rewritten; journal line on main; branch deleted
