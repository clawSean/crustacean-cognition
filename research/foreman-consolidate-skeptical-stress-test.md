# Architecture Review B — Skeptical Stress-Test of Adding Consolidate

## Context

JPop / Sean want to add a fifth pipeline stage — **Consolidate** — between Curate and Compile, motivated by `MEMORY.md` exceeding its ~500-line working-memory cap (currently ~523 lines). The accepted proposal in `PROJECT_PROGRESS.md` keeps Consolidate separate from Compile, scopes it as a **mover/indexer** (verbatim relocation + pointer + manifest), runs it on Sonnet/gpt-5.5 twice weekly, and tells Compile to read recent manifests as Phase 0.

This review acts as a **skeptical reviewer** of that proposal. It does not relitigate the separation decision (Foreman's reasoning holds: Compile is weekly synthesis, not 6-day-stale hot-path hygiene). It assumes separation is correct and stress-tests the spec for ways the v1 design will quietly destroy context, hide material from Compile, over-prune working memory, or compound telephone-game drift.

**Verdict up front:** Separation is sound, but the spec as written has four leaks that will compound into context loss within ~3–6 runs unless plugged before v1 ships. Ship Consolidate, but tighten the spec, sequence the rollout, and constrain the first run aggressively.

---

## Critique — Where Consolidate Can Quietly Break Things

### Leak 1 — "Verbatim where possible" is a loophole

`PROJECT_PROGRESS.md` says "preserve moved content verbatim where possible." That phrasing leaves a back door. A Sonnet/gpt-5.5 run will route around an awkward straddling section by lightly rewriting "to make the pointer cleaner" — that is synthesis, not relocation, and the loss is invisible until Calibrate spot-checks weeks later.

**Fix:** make verbatim a hard invariant. Byte-equal moves only, with a single appended provenance line at the destination (`*Relocated from MEMORY.md by Consolidate on YYYY-MM-DD.*`). No other rewrite, ever — not even whitespace normalization, not even "fixing" a markdown heading level. Verify byte-equality in the manifest as part of the preservation check.

### Leak 2 — No pre-state snapshot in the recovery story

The spec says pre-Consolidate content is recoverable from "MEMORY.md + relocation destinations + manifest." But destinations are mutable: Curate writes to them daily, Compile graduates them weekly, humans edit them. A manifest that points at `knowledge/topics/foo.md` without capturing **what was at that location at relocation time** is a stale receipt. Three runs later, recovery is no longer possible — which means the preservation invariant degrades silently.

**Fix:** each run snapshots `MEMORY.md` pre-state to `archive/consolidate-manifests/YYYY-MM-DD-HHMM-pre.md` and content-hashes both source section and destination after-state. The pre-snapshot is the recovery floor; the hashes are what Compile/Calibrate use to detect drift.

### Leak 3 — The within-MEMORY.md blocklist is implicit

The spec correctly forbids editing the foundation files (`AGENTS.md`, `SOUL.md`, `IDENTITY.md`, `USER.md`, `TOOLS.md`). But `MEMORY.md` itself contains foundation-equivalent surfaces, and a generic "stable for 2+ weeks → move" heuristic catches them all:

- `## 📂 Memory Structure Map` — the index of indexes; corruption breaks retrieval everywhere.
- `## 🔥 Hot / Time-Sensitive` — by definition working memory; "stable" doesn't apply (an overdue item like the Mar 23 Obsidian setup is intentionally on the human-attention surface, not stable knowledge).
- `## 🔐 Security Rules` — operational policy.
- `## 🏷️ Trust Tiers` — policy, not knowledge.
- Behavioral rules in `## 💬 Communication Guidelines` (the "CRITICAL — relaying messages" lesson is AGENTS-territory).
- `## 📋 Active Cron Jobs` — operational state, not stable knowledge.
- `## 🦞 Identity & Setup` model config — operational state.
- Any line beginning with `**Firm rule:**` / `**Rule:**`.

**Fix:** the spec must enumerate untouchable sections inside `MEMORY.md` by heading, not infer them from a stability heuristic.

### Leak 4 — Compile's Phase 0 manifest reading is hand-waved

`PROJECT_PROGRESS.md` says "Compile reads recent Consolidate manifests as Phase 0." That single sentence is the linchpin of the no-telephone-game claim — but it's not specified. Without:

- enumerate-since-last-Compile semantics,
- explicit promise that Compile treats relocated files as in-scope for lesson extraction (Phase 4) and graduation (Phase 5),
- a hash-continuity check (manifest hash vs destination current hash),

…Compile will silently lose visibility into a growing fraction of the workspace. Within a quarter, that's most of the technical content.

**Fix:** specify Phase 0 concretely in `COMPILE.md` (see doc-edit guidance below).

### Other failure modes (quicker takes)

- **Cron-time race.** Curate (6:00 AM PT daily) + Consolidate (proposed Wed/Sat ~5 AM PT) can interleave. Sequence: Consolidate at 5:30 AM PT, Curate at 6:00 AM PT, never overlap windows; lock `MEMORY.md` for the duration.
- **Promotion-threshold conflict.** `COMPILE.md` Phase 2 promotes at 10-lines / 5-mentions. Consolidate uses a line-cap target. If thresholds disagree, sections fight each other across passes. Consolidate should adopt identical entity thresholds; Compile Phase 2 collapses to "verify Consolidate met its goal."
- **Pointer format drift.** Free-form pointer wording will diverge across runs. Lock template: `→ Moved to \`<path>\` (Consolidate YYYY-MM-DD).` Greppable, parseable, no prose.
- **Section-straddle splits = synthesis.** If a section mixes hot + stable content, the only honest move is to flag and skip. Splitting requires deciding what "belongs" — that's Compile's graduation job.
- **Foundation-file recommendations as silent drift.** Recommendations sit in manifests; Calibrate runs monthly. Three Consolidates between calibrations = stale queue. Add a single rolling `archive/consolidate-manifests/RECOMMENDATIONS.md` that Calibrate must drain.
- **First-run blast radius.** Going from 523 → 350 lines in one pass moves ~170 lines, right at the proposed 200-line cap, on the run with the least operational history. Worst possible run to be aggressive on. First applied run should cap at ≤100 lines and tolerate not hitting the floor.
- **Hot/overdue reclassification.** "Stable" ≠ "irrelevant." Overdue Hot items belong on the human-attention surface, not in `archive/` — Consolidate must not touch them.
- **Idempotency / line-counting.** "≤350 lines" must be precisely defined: does it include code fences? table rows? front-matter? Define once in `CONSOLIDATE.md`.
- **Atomicity.** Manifest written first, all-or-nothing on writes, rollback on partial failure. A crashed run must leave `MEMORY.md` either entirely pre-state or entirely post-state, never half.
- **Destination directory creation.** If a needed destination doesn't exist, flag for human; do not auto-create. Auto-creation grows the structure map silently.

---

## Hard Guardrails (the v1 must-have list)

1. **Scope locked to MEMORY.md.** No `contacts/`, `groups/`, `notes/` touching until ≥4 successful runs without rollback or drift warning.
2. **Section-boundary moves only.** Whole markdown sections (`##` or `###`). No splits.
3. **Foundation file blocklist.** `AGENTS.md`, `SOUL.md`, `IDENTITY.md`, `USER.md`, `TOOLS.md` — read-only, recommendations only.
4. **Within-MEMORY.md blocklist (enumerated):**
   - `## 📂 Memory Structure Map` and all subsections
   - `## 🔥 Hot / Time-Sensitive`
   - `## 🔐 Security Rules`
   - `## 🏷️ Trust Tiers`
   - `## 💬 Communication Guidelines`
   - `## 📋 Active Cron Jobs`
   - `## 🦞 Identity & Setup`
   - Any line starting with `**Firm rule:**` / `**Rule:**`
5. **Move cap:** ≤150 lines per pass; **first applied run ≤100 lines.**
6. **Floor / tripwire:** ≤350 lines → no-op; 350–400 → optional smallest viable move; >400 → required.
7. **Pointer template (locked):** `→ Moved to \`<path>\` (Consolidate YYYY-MM-DD).`
8. **Verbatim invariant:** byte-equal except for one appended provenance line at destination.
9. **Manifest is canonical & atomic:** written first, every move logged with source heading + line range + source SHA-256 + destination + destination SHA-256-after + pointer text. Pre-state snapshot of `MEMORY.md` archived. Rollback on partial failure.
10. **Cron sequencing:** Consolidate runs before Curate on shared days; lock `MEMORY.md` during run.
11. **Compile Phase 0 spec required:** enumerate manifests since last Compile, treat relocated files as in-scope for lesson extraction and graduation, verify hash continuity, surface drift in weekly digest.
12. **No new destination directories.** Flag for human if missing.
13. **Dry-run gate.** First applied run preceded by dry-run reviewed by JPop.
14. **Recovery rehearsal.** Acceptance test: reconstruct pre-Consolidate `MEMORY.md` byte-equal from manifest + pre-snapshot + destinations.
15. **Drift detector in Compile.** Each weekly Compile re-hashes destinations cited by recent manifests; logs telephone-game warnings when destination has drifted from manifest hash without explanation.
16. **Recommendations queue.** All foundation-file flags accumulate in `archive/consolidate-manifests/RECOMMENDATIONS.md`; Calibrate must drain it.

---

## Manifest Specification

```yaml
---
consolidate_run: 2026-05-03T05:30:00-07:00
mode: dry-run | applied
memory_md_lines_before: 523
memory_md_lines_after: 451
move_cap_lines: 150
first_run: true
preservation_invariant: verified
pre_state_snapshot: archive/consolidate-manifests/2026-05-03-053000-pre.md
---

# Consolidate Manifest — 2026-05-03 05:30 PT

## Summary
- Lines moved: 72
- Sections moved: 3
- Recommendations queued: 2

## Moves

### Move 1
- source_section: "## 🔬 Technical Insights"
- source_lines: 322–409
- source_sha256: <hash>
- destination: knowledge/topics/memory-md-technical-insights-2026-05.md
- destination_action: created
- destination_sha256_after: <hash>
- pointer_inserted: "→ Moved to `knowledge/topics/memory-md-technical-insights-2026-05.md` (Consolidate 2026-05-03)."
- preservation_check: byte-equal verified

[…]

## Recommendations (→ archive/consolidate-manifests/RECOMMENDATIONS.md)
- AGENTS.md may benefit from absorbing "Firm rule" lines from MEMORY.md OpenClaw Knowledge Practice section.
- Hot section has 6 items > 14 days overdue; Curate or human triage recommended.
```

The manifest, the pre-state snapshot, and the running `RECOMMENDATIONS.md` are the three artifacts that make Consolidate auditable. Calibrate must verify all three exist and are coherent each month.

---

## Concrete First-Run Targets (from current MEMORY.md, ~523 lines)

Order: safest first. Total target ~106 lines moved → MEMORY.md ~417 lines (above the 400 tripwire — that's fine, converge in stages, do not chase the floor on run #1).

### Move 1 — Pass-log compaction (~17 lines saved)

- **Source:** lines 506–523 (the trail of `*Curate pass:` / `*Compile pass:` / `*Previous restructures:` italicized lines).
- **Destination:** keep last 2 entries inline in `MEMORY.md`; relocate the rest verbatim to:
  - `archive/digests/curate-log.md`
  - `archive/digests/compile-log.md`
- **Why safe:** append-only history rows; exactly the bloat Consolidate should evict; no semantic content lost.

### Move 2 — Technical Insights section (~85 lines saved)

- **Source:** `## 🔬 Technical Insights (updated 2026-02-12)` — lines 322–409.
- **Destination:** `knowledge/topics/memory-md-technical-insights-2026-05.md` (new file, whole section verbatim).
- **Pointer left:** one pointer line plus a 5–8 line bullet list of subsection titles (so future readers can recognize what's there before clicking through). The bullet list is *not* synthesis — it's a literal copy of the `**Subheading**` lines from the moved section.
- **Why safe:** the textbook target — factual, dated, mostly stable. Items range from genuinely transferable (Brave free-tier rate limit, Claude API plan limitations, NVIDIA Kimi config, zero-token plugin pattern, Telegram bot group permissions, debounce/queue interrupt mechanics, .skill format, Memory Search Architecture audit) to operational-state-but-still-valuable (Bootstrap truncation 2026-04-28, Venice 402, Kimi K2.5 status). Verbatim relocation preserves them all; **Compile** later does the splitting and graduation.
- **Critical:** Consolidate must NOT split this into per-topic files. Single-file landing. Splitting is graduation; graduation is Compile's job.

### Move 3 — Los Crabos detail compaction (~4 lines saved)

- **Source:** the "Los Crabos" inline-entity bullet (around line 106) carrying ~4–5 lines of duplicated detail (twin crab-kings canon, members, gaming backlog) that already lives in `memory/groups/telegram--1003584500340.md`.
- **Action:** verify duplication via hash spot-check of destination; if confirmed, reduce inline to a single pointer line.
- **Why safe:** removing duplication, not unique content.
- **Critical:** if the destination is missing any line of unique content, Consolidate must abort the move and flag — don't lose unique content to a sloppy dedupe.

### What NOT to move in run #1

- `📂 Memory Structure Map` (incl. People table, Inline Entities header, Promotion Rule)
- `🔥 Hot / Time-Sensitive` (even overdue items)
- `🔐 Security Rules`
- `🏷️ Trust Tiers`
- `💬 Communication Guidelines`
- `📋 Active Cron Jobs` table + Known Issues
- `🦞 Identity & Setup`
- `🎨 Image Generation` (defer to run #2)
- `🔍 Research Interests` (defer to run #2)
- `📚 OpenClaw Knowledge Practice` — contains a Firm rule + a dated decision; defer to run #2 for the dated decision sub-block only
- Any inline entity that hasn't been verified as already-canonicalized elsewhere

### Run #2 candidates (after run #1 validated, ≥1 Compile cycle has consumed manifest)

- `🎨 Image Generation` → `knowledge/procedures/image-generation.md` (~18 lines)
- `🔍 Research Interests` → `knowledge/topics/privacy-defi-landscape.md` (~22 lines; the "ongoing with Dan" block stays in his contact file via a separate Compile graduation step, not Consolidate)
- "Memory architecture decision (2026-02-25)" sub-block → `knowledge/procedures/memory-architecture.md` (~6 lines)

That puts MEMORY.md at ~370 lines after two runs — under tripwire, above floor, with no synthesis performed by Consolidate.

---

## Doc-Edit Guidance (recommendations only — do not edit yet)

### `pipeline/CONSOLIDATE.md` (new file)

Sections to include, in order:

1. **Purpose** — Mover/indexer for `MEMORY.md` hygiene. Not a synthesizer.
2. **Cadence & runtime** — Wed + Sat 5:30 AM PT, gpt-5.5, isolated session, ~5–8 min.
3. **Scope (v1)** — `MEMORY.md` only. v2 expansion gated on ≥4 clean runs.
4. **Forbidden surfaces** — foundation-file blocklist + within-MEMORY.md blocklist (both enumerated above).
5. **Allowed sources** — whole markdown sections only (`##` / `###` boundary).
6. **Verbatim invariant** — byte-equal moves; one appended provenance line at destination.
7. **Pointer template** — locked.
8. **Move cap & floor** — ≤150 lines/pass, first run ≤100; ≤350 → no-op; 350–400 → optional; >400 → required.
9. **Manifest spec** — canonical, atomic, hashed, pre-state snapshot.
10. **Recovery rehearsal** — acceptance criterion.
11. **Recommendations queue** — append to `archive/consolidate-manifests/RECOMMENDATIONS.md`.
12. **What NOT to do** — no synthesis, no graduation, no foundation edits, no deletion, no destination creation.

### `pipeline/CURATE.md`

- Tighten `MEMORY.md` inbound: max ~5 lines per new entry; large/factual content routes to canonical destination with pointer.
- New rule: if Curate sees a Consolidate pointer line, treat the destination as source of truth — never reflate pointer back into content.
- Tighten Routing Decision Tree: the `MEMORY.md` row now says `Hot/current AND ≤5 lines AND no canonical home exists yet`.

### `pipeline/COMPILE.md`

- **New Phase 0 — Read Consolidate manifests.**
  - Enumerate `archive/consolidate-manifests/*.md` since last Compile run.
  - For each move: treat relocated file as in-scope for Phase 4 (lesson extraction) and Phase 5 (graduation).
  - Verify hash continuity: compare manifest's `destination_sha256_after` to current destination hash; flag drift in weekly digest.
- **Phase 2 (Entity Promotion) shrinks** to: verify Consolidate met line-cap goal; if `MEMORY.md > 400`, escalate in digest. No longer the primary owner of `MEMORY.md` hygiene.
- **Phase 3 (Staleness Pruning) keeps non-MEMORY.md surfaces** (Consolidate never prunes).
- Compile retains: archival, lesson extraction, graduation (incl. splitting Consolidate's monolithic landings into proper per-topic files), contradictions, patterns, backlinks, weekly digest.
- Digest template gets two new lines: "Consolidate manifests processed: [list]" and "Drift warnings: [count]."

### `pipeline/CALIBRATE.md`

- New phase **Consolidate Audit** — review month's manifests, sample-check preservation invariant, drain `RECOMMENDATIONS.md`, propose foundation-file edits.
- Refocus per JPop's direction: lesson validation, missed-lesson scan from monthly daily files, procedure gap-fill, skill notes updates.
- Reduce foundation/pipeline drift checks to "light" — Consolidate audit + recommendations queue cover most of the surface previously addressed by deep foundation review.

### `pipeline/HOW-IT-WORKS.md`

- 4C → 5C throughout: diagrams, tables, prose.
- Insert Consolidate stage between Curate and Compile, with manifest arrow flowing forward to Compile and Calibrate.
- Add a **Preservation Chain** callout: Consolidate produces manifest + pre-state snapshot → Compile reads manifests + verifies hashes → Calibrate audits manifests + drains recommendations.
- Update Timing Summary table (add Consolidate row), Setup & Configuration cron list, Isolated Sessions table.
- Add explicit boundary statement: **mover/indexer (Consolidate) vs synthesizer (Compile).** Anything that requires deciding what's stable, transferable, a lesson, a contradiction, or a pattern is Compile's job.

### `MEMORY.md` (one-line human edit before first run, NOT a Consolidate target)

- Promote the `Promotion Rule` thresholds (10 lines / 5 mentions) to be explicitly tagged as the canonical entity-promotion thresholds shared by Consolidate and Compile, so the two pipelines can't diverge later.

---

## Decisive Recommendations

1. **Ship Consolidate, but not until the four spec leaks are closed**: verbatim invariant, pre-state snapshot, within-MEMORY.md blocklist, Compile Phase 0 spec.
2. **First run is dry-run only**, reviewed by JPop, manifest validates against the spec.
3. **First applied run targets ≤100 lines** (Moves 1–3 above). Don't chase the 350 floor in one shot.
4. **`COMPILE.md` Phase 0 ships in the same change set** as `CONSOLIDATE.md`. They are one system, not two independent jobs.
5. **Acceptance test:** reconstruct pre-Consolidate `MEMORY.md` byte-equal from manifest + pre-snapshot + destinations.
6. **Hold v2 expansion** (contacts/groups/profile drift) until ≥4 successful Consolidate runs with no rollbacks and no drift warnings logged by Compile.
7. **Cron timing:** Wed + Sat 5:30 AM PT (before Curate's 6:00 AM PT). gpt-5.5 model lane for parity with current 4C jobs. `wakeMode: next-heartbeat` per the cron-safety note already on file.
8. **Restate the boundary in every doc:** Consolidate moves; Compile decides. Any Consolidate run that requires a "what does this content really mean" judgment is malformed and should abort with a flag to Compile.

---

## Verification Plan (how to validate before declaring v1 done)

1. **Dry-run on current MEMORY.md** produces a manifest that:
   - lists Moves 1, 2, 3 from the first-run target list above,
   - emits exactly the locked pointer template,
   - includes pre-state snapshot path + source/destination hashes,
   - leaves `MEMORY.md` un-edited (mode: dry-run),
   - flags zero forbidden-surface touches.
2. **Human review** of dry-run manifest by JPop; approve or revise targets.
3. **Applied run** under same input; verify byte-equal between dry-run plan and applied result.
4. **Recovery rehearsal:** reconstruct pre-Consolidate `MEMORY.md` from artifacts; diff against pre-snapshot; require zero diff.
5. **First Compile after v1** runs Phase 0 and produces digest lines for manifests processed + drift warnings (expect zero drift on first cycle).
6. **First Calibrate after v1** processes `RECOMMENDATIONS.md`, even if empty, to validate the queue path.
7. **Run #2 only after #1 passes all of the above.**
