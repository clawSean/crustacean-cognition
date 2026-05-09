# 5C Pipeline Deploy — Review Notes

Sync of finalized 5C memory-system pipeline docs from
`/root/projects/clawSean/crustacean-cognition/pipeline/` into the live runtime
at `/root/.openclaw/workspace/memory/.system/`.

Branch: `feature/calibrate-5c-lane-balancing`

---

## Phase 1: Drift Findings

For each live file, the live copy was diffed against the repo copy to detect
unexpected live edits before overwriting.

### CALIBRATE.md

- **Live state:** Original v1 (Mar 16 2026, 4472 bytes), framed as a 4C "deep
  monthly review."
- **Repo state:** Finalized 5C version (May 3 2026, 8035 bytes).
- **Drift assessment:** No live-only edits detected. The live file is strictly
  an older version; every change in the diff is part of the planned 5C
  upgrade — added Lane Boundaries section, expanded foundation-file phases,
  reframed pipeline-health audit around 5C with explicit Consolidate review,
  Lessons-Validation lane scoping (Calibrate works from Compile output, does
  not re-extract).
- **Conclusion:** Safe to overwrite.

### COMPILE.md

- **Live state:** Original v1 (Mar 17 2026, 6688 bytes), single-pass weekly
  job covering organization + extraction.
- **Repo state:** Finalized 5C version (May 3 2026, 10551 bytes).
- **Drift assessment:** No live-only edits detected. Diff shows the planned
  upgrade only: added Lane Boundaries, mandatory Phase 0 Consolidate manifest
  review, Context Pressure Protocol with explicit phase-priority order,
  reframed Phase 1/2/3 as judgment-only sweeps with caps, and tightened
  graduation language. Mechanical bulk hygiene was explicitly punted to
  Consolidate.
- **Conclusion:** Safe to overwrite.

### CURATE.md

- **Live state:** Original v1 (Mar 16 2026, 3811 bytes).
- **Repo state:** Finalized 5C version (May 3 2026, 6445 bytes).
- **Drift assessment:** No live-only edits detected. Diff shows the planned
  upgrade: added Lane Boundaries; explicit "Curate routes; Compile
  synthesizes" framing; new `MEMORY.md` Write Rules section with
  1–3 line default and 5-line hard cap; routing decision table updated to
  steer most material to canonical homes rather than `MEMORY.md`; explicit
  rule that maturity-uncertain items go to `memory/notes/` rather than
  `knowledge/`.
- **Conclusion:** Safe to overwrite.

### HOW-IT-WORKS.md

- **Live state:** Original 4C narrative (Mar 16 2026, 31345 bytes).
- **Repo state:** Finalized 5C narrative (May 3 2026, 21976 bytes — smaller
  because long psychology-grounding prose was trimmed).
- **Drift assessment:** No live-only edits detected. Diff shows the planned
  upgrade: 4C → 5C throughout, Consolidate inserted between Curate and
  Compile in both the architecture diagram and the staged sections, new
  Lane Separation Summary table at the bottom, file-structure block updated
  to show CONSOLIDATE.md, and removal of the older academic-grounding /
  "four systems working together" sub-narrative which is now redundant with
  the tighter framing.
- **Conclusion:** Safe to overwrite.

### COLLECT.md

- **Live state:** Mar 17 2026, 2336 bytes.
- **Repo state:** Mar 17 2026, 2336 bytes.
- **Drift assessment:** Identical content. md5 `bb510fecdef3adf5e44fe4fa0e299e90`
  on both sides. Not in scope for this sync — left untouched.

### CONSOLIDATE.md

- **Live state:** Did not exist.
- **Repo state:** New file (May 3 2026, 7081 bytes).
- **Conclusion:** New install — no drift possible.

### Overall

The live `.system/` directory was an older snapshot of the same docs; no
hand-edits had accumulated against it since the prior deploy. A clean
overwrite was therefore safe.

---

## Phase 2: Sync Manifest

Files synced from `/root/projects/clawSean/crustacean-cognition/pipeline/`
to `/root/.openclaw/workspace/memory/.system/` on 2026-05-04.

| File | Source MD5 | Destination MD5 (post-copy) | Bytes | Status |
|---|---|---|---|---|
| CALIBRATE.md | `7b698d1f51ba23595f75263169ba28ee` | `7b698d1f51ba23595f75263169ba28ee` | 8035 | match |
| COMPILE.md | `17b9ba7e03d4adbb4b0d93d227d19122` | `17b9ba7e03d4adbb4b0d93d227d19122` | 10551 | match |
| CURATE.md | `59ed4145daa511243739e4dc2b53c542` | `59ed4145daa511243739e4dc2b53c542` | 6445 | match |
| HOW-IT-WORKS.md | `777868ccc3534d8ac15619adac4bc11d` | `777868ccc3534d8ac15619adac4bc11d` | 21976 | match |
| CONSOLIDATE.md (new) | `e7555d9565c3398ab9eed45a17ffec0d` | `e7555d9565c3398ab9eed45a17ffec0d` | 7081 | match |

Pre-sync live hashes (replaced):

| File | Replaced MD5 |
|---|---|
| CALIBRATE.md | `27167e0d98d585012331c1019aaa8e94` |
| COMPILE.md | `441dcf759f2fed4130c4bbcf4f32286e` |
| CURATE.md | `e9f5ba477b77162e85b30ad3b33f9e37` |
| HOW-IT-WORKS.md | `42d0d9f7338dbcf0d4192cefd2ce769a` |
| CONSOLIDATE.md | (did not exist) |

Files explicitly NOT touched:

- `COLLECT.md` — unchanged in this update; live md5
  `bb510fecdef3adf5e44fe4fa0e299e90` matches repo md5.
- All cron jobs and other workspace contents — out of scope.

Verification performed:

- md5sum on all 5 destination files matched source.
- File sizes on disk matched source exactly.
- First line of each destination file inspected (header sanity check).
- Directory listing confirms 6 files now present:
  CALIBRATE, COLLECT, COMPILE, CONSOLIDATE, CURATE, HOW-IT-WORKS.

---

## Concerns, Caveats, and Follow-ups

- **No COLLECT.md change in this batch by design.** The 5C update did not
  alter the Collect lane. If COLLECT.md ever needs a 5C cross-reference
  (e.g., explicit handoff to Consolidate vs. Curate), that would be a
  follow-up.
- **No cron edits performed.** The new Consolidate stage exists as a doc
  only after this sync. To actually run it, a cron entry pointing at
  `CONSOLIDATE.md` will need to be added separately. That is intentionally
  outside the scope of this deploy.
- **No pipeline test runs were triggered.** The instructions live in
  `.system/`; the next scheduled Curate/Compile/Calibrate run will pick them
  up automatically. Worth watching the next Compile digest for the new
  Phase 0 manifest-review behavior — if Compile complains about a missing
  `archive/consolidate-manifests/` directory, that directory may need to be
  pre-created (empty) or Compile's Phase 0 needs a "no-manifests-yet"
  graceful path. Flag for Calibrate if it surfaces.
- **HOW-IT-WORKS.md got smaller (31345 → 21976 bytes).** This is expected
  (Tulving/Squire grounding prose trimmed in favor of tighter framing) but
  worth noting because byte-shrinking syncs sometimes look like accidental
  truncation. Diff confirms intentional content restructuring, not
  truncation.
- **MEMORY.md write rules tightened.** New 5-line hard cap in CURATE.md is
  stricter than prior live behavior. Watch the first 1–2 Curate runs for
  over-aggressive trimming or routing churn. If problems appear, Calibrate
  should reconsider the cap rather than Curate quietly ignoring it.

---

## Sync Status

**Sync complete: 2026-05-04**

5 files written, all verified by md5 and size. COLLECT.md untouched. No cron
or pipeline mutations. Live `.system/` is now byte-equal to repo `pipeline/`
for the in-scope files.
