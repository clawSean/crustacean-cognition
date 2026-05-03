# Memory Pipeline Consolidate Initiative — PROJECT_PROGRESS

**Status:** scoped
**Started:** 2026-05-02 23:40 UTC
**Owner:** JPop / Sean
**Context:** `MEMORY.md` is exceeding its intended ~500-line working-memory/index role. Need a cleaner division of responsibilities across the memory pipeline.

---

## Goal

Add a dedicated memory-pipeline step that keeps `MEMORY.md` lean by moving cooled/stable content into the correct long-term storage, while keeping Compile and Calibrate focused on higher-value learning work.

Working name: **C3.5 Consolidate**
Preferred architecture name: **5C pipeline** — Collect, Curate, Consolidate, Compile, Calibrate

Accepted architecture decision:

> Keep Consolidate separate from Compile. Consolidate is a conservative mover/indexer. Compile remains the weekly synthesis/finalization pass.

---

## What We Learned

### 0. Fresh Foreman decision on Consolidate vs Compile separation

A second narrow Claude Foreman Opus pass focused only on whether Consolidate should stay separate from Compile concluded: **keep Consolidate separate**.

Core reasoning:

- `MEMORY.md` bloat is a hot-path daily/weekly pressure; weekly Compile cannot keep working memory healthy for the other six days.
- Compile's value is synthesis: lessons, graduation, contradictions, patterns, backlinks, digest.
- Consolidate should be a lightweight **mover/indexer**, not a summarizer/synthesizer.
- Folding routine hygiene into Compile would bloat Compile and spend Opus on Sonnet-grade work.

Important refinement from this pass:

- Consolidate should preserve rich context by **verbatim relocation + pointer + manifest**, not by compressive rewriting.
- Compile should read recent Consolidate manifests as Phase 0 input, so it knows what moved and where to inspect.
- This directly addresses telephone-game risk: Consolidate moves detail; Compile synthesizes from preserved detail.

Reference plan:

- `/root/.claude/plans/think-deeply-about-one-humble-milner.md`

### 0.1 Final multi-Foreman design review — first-run criteria + spec hardening

JPop requested 2+ Opus / max-deliberation Foreman passes over the full 4C docs, current `MEMORY.md`, and this progress doc. Three were launched:

- Review A: candidate identification / guiding criteria — partially completed, then hit Claude CLI limit.
- Review B: skeptical stress-test — completed enough to produce a plan, then hit limit.
- Review C: implementation-spec lens — completed enough to produce a detailed final-edit plan, then hit limit.

Saved copies in this repo:

- `research/foreman-consolidate-vs-compile-decision.md`
- `research/foreman-consolidate-skeptical-stress-test.md`
- `research/foreman-consolidate-implementation-spec.md`

Key upgraded guidance from Reviews B/C:

- Consolidate remains separate from Compile, but v1 must be **more conservative** than the first sketch.
- Consolidate v1 scope is **`MEMORY.md` only**. Do not touch contacts/groups/profile drift until after at least 4 clean Consolidate runs with no rollback/drift warnings.
- Consolidate is **byte-preserving relocation**, not summarization: whole sections only, no section splitting, no markdown cleanup, no light rewriting.
- Every run needs a manifest plus a **pre-state snapshot** of `MEMORY.md`; this is the recovery floor if destinations later change.
- Compile Phase 0 is non-negotiable and must ship with Consolidate: read manifests since last Compile, inspect destinations, verify hashes/drift, and include moved material in lesson/graduation/pattern synthesis.
- First applied run should move **≤100 lines**, not chase the 350-line target immediately.
- Normal move cap should be **≤150 lines/pass** (stricter than the earlier 200-line sketch).
- Consolidate must use locked pointer wording, e.g. `→ Moved to \`<path>\` (Consolidate YYYY-MM-DD).`
- Consolidate must not auto-create new destination directories in v1; if a home is unclear/missing, flag instead of inventing structure.
- Consolidate recommendations for foundation files should accumulate in `archive/consolidate-manifests/RECOMMENDATIONS.md` for Calibrate/human review.

First-run MEMORY.md candidates from Foreman stress-test:

1. **Pass-log compaction** — move old trailing `Curate pass` / `Compile pass` lines to archive/digest logs; keep only the newest 2–5 inline.
2. **Technical Insights section** — move the whole section verbatim to one landing file, e.g. `knowledge/topics/memory-md-technical-insights-2026-05.md`; leave a pointer plus copied subsection-title list. Do **not** split it into per-topic files; splitting/graduation belongs to Compile.
3. **Los Crabos inline detail compaction** — only if destination group file already contains all unique detail; otherwise skip and flag.

What v1 must not move:

- `## 📂 Memory Structure Map`
- `## 🔥 Hot / Time-Sensitive`
- `## 🔐 Security Rules`
- `## 🏷️ Trust Tiers`
- `## 💬 Communication Guidelines`
- `## 📋 Active Cron Jobs`
- `## 🦞 Identity & Setup`
- any `**Firm rule:**` / `**Rule:**` lines
- any mixed hot/stable section requiring judgmental splitting

Guiding principle now accepted:

> Consolidate moves; Compile decides. If a move requires deciding what content *means*, whether it is a lesson, whether it is transferable knowledge, or how to split it, Consolidate should skip/flag and let Compile handle it.

### 0.2 Curate vs Consolidate is not promote vs demote

JPop flagged a conceptual concern: Curate promotes content into `MEMORY.md`, profiles, and memory files; Consolidate immediately after that can sound like it does the opposite.

Refined mental model:

- **Curate promotes from raw capture into canonical homes.** It reads daily logs and decides what deserves durable storage.
- **`MEMORY.md` is only one Curate destination, and should be the hot/index lane.** Full detail should usually go to contacts/groups/lessons/goals/knowledge/notes, with `MEMORY.md` receiving only concise hot context or a pointer.
- **Consolidate does not reverse Curate's promotion.** It corrects `MEMORY.md` bloat when hot/index content has cooled or when full detail accidentally landed in working memory.
- **Consolidate v1 should not touch canonical profiles/memory files.** Contacts/groups/profile compaction is a later, separate concern after `MEMORY.md` consolidation proves safe.
- On shared days, **Consolidate should run before Curate** in wall-clock time (e.g. 5:30am vs Curate 6am), so it cleans up prior accumulated working-memory drift rather than immediately undoing fresh Curate output.

Better phrasing:

> Curate promotes raw experience into durable memory. Consolidate preserves durable memory while keeping the working-memory index from becoming a warehouse.

### 1. Current pipeline has a structural eviction gap

The existing system has producers but no clear eviction owner:

- **Collect** captures raw session/activity context into daily files.
- **Curate** routes daily material into long-term places and sometimes into `MEMORY.md`.
- **Compile** does weekly organization, lesson extraction, graduation, pattern recognition, and digest writing.
- **Calibrate** does monthly foundation/pipeline/lesson review.

Problem: `MEMORY.md` receives hot/current content, but nothing specifically owns moving content back out once it cools or stabilizes. Compile has some promotion/pruning language, but its scope is too broad and its triggers are too narrow.

### 2. `MEMORY.md` should be working memory + index, not a warehouse

The architecture says `MEMORY.md` should be capped around ~500 lines and function as:

- current hot context,
- compact index/pointers,
- immediately useful working memory.

It should not hold large stable sections like long technical insights, cron reference, old pass logs, or durable procedures. Those belong in `knowledge/`, `memory/`, `archive/`, or review queues depending on type. Foundation/operator files such as `AGENTS.md`, `TOOLS.md`, `USER.md`, `IDENTITY.md`, and `SOUL.md` are not direct-edit targets for Consolidate; Consolidate may only flag recommendations for Calibrate/human review.

### 3. C3.5 Consolidate is a good fit

Claude Foreman on Opus recommended adding a distinct **Consolidate** step rather than expanding Compile.

Reasoning:

- Consolidation has a different cadence than Compile.
- It can use Sonnet rather than Opus.
- It gives single responsibility: keep `MEMORY.md` healthy.
- It lets Compile focus on weekly learning and pattern synthesis.
- It lets Calibrate focus on lessons/procedures, matching JPop's direction.

### 4. Calibrate should become more learning-focused

JPop explicitly wants Calibrate focused on learnings, reading, and writing to relevant locations where appropriate.

Recommended Calibrate scope:

- validate existing `memory/lessons/`,
- scan monthly daily files for missed lessons,
- write/update `knowledge/procedures/` when procedural knowledge recurs,
- update `skills/<skill>/NOTES.md` for recurring skill/tool friction,
- perform only light foundation/pipeline drift checks.

### 5. Compile should be slimmed down

Compile should keep the Opus-grade weekly work:

- archive old daily files,
- extract lessons from the week,
- graduate memory into knowledge,
- identify recurring patterns, contradictions, and backlinks,
- write weekly digest.

Compile should no longer be the primary owner of `MEMORY.md` pruning/promotion once Consolidate exists.

---

## Proposed Responsibility Split

### Collect

No change. Raw capture into `memory/daily/`.

### Curate

Daily inbound routing. Tighten rules for `MEMORY.md`:

- write only concise hot/current entries,
- target max ~5 lines per new entry,
- prefer explicit expiration/current-action relevance,
- route stable or large content directly to long-term storage,
- leave a pointer in `MEMORY.md` only when useful.

### Consolidate

New step. Initial primary input: `MEMORY.md`.

Mandate:

- keep `MEMORY.md` under control,
- target ≤350 lines, warning/tripwire above 400,
- move stable/cooled content out of working memory,
- preserve moved content verbatim where possible,
- leave one-line pointers behind,
- write a per-run manifest of every move,
- rotate old pass logs into digests/archive.

Critical boundary:

- Consolidate is a **mover/indexer**, not a summarizer/synthesizer.
- It should not extract lessons, resolve contradictions, graduate memory to knowledge, or edit foundation files directly.

Candidate cadence/model:

- **2× weekly** — Wednesday + Saturday around 5am PT,
- **gpt-5.5 initially** to match the currently configured 4C jobs,
- optional later lane: Sonnet/Claude CLI if/when the subscription-backed cron pattern is designed,
- isolated session,
- expected runtime ~5–8 minutes.

Safety mechanics:

- first run is dry-run/plan + diff only,
- every applied run writes `archive/consolidate-manifests/YYYY-MM-DD-HHMM.md`,
- every applied run snapshots pre-state to `archive/consolidate-manifests/YYYY-MM-DD-HHMM-pre.md`,
- move at most ~150 lines per pass,
- first applied run moves at most ~100 lines,
- if `MEMORY.md` is ≤350 lines, produce a no-op manifest and stop,
- never delete; only move/preserve/pointer,
- preservation invariant: pre-Consolidate content must be recoverable from `MEMORY.md` + relocation destinations + manifest + pre-state snapshot,
- use source/destination hashes in the manifest so Compile and Calibrate can detect drift.

### Compile

Weekly Opus pass focused on learning and synthesis, not routine `MEMORY.md` hygiene.

New expected input if Consolidate exists:

- read recent Consolidate manifests as Phase 0, so moved content remains visible to weekly synthesis.

Compile keeps:

- daily-file archival,
- lesson extraction,
- memory → knowledge graduation,
- contradiction/pattern/backlink work,
- weekly digest.

Compile sheds:

- routine `MEMORY.md` hygiene,
- direct responsibility for working-memory size control.

### Calibrate

Monthly Opus pass focused on learning quality:

- lessons validation,
- missed-lesson extraction,
- procedure gap-fill,
- skill notes updates,
- light pipeline/foundation review only.

---

## Proposed File Changes

Need to update/create:

- `memory/.system/CONSOLIDATE.md` — new instructions for the C3.5 step.
- `memory/.system/HOW-IT-WORKS.md` — add Consolidate to diagrams/tables/descriptions.
- `memory/.system/CURATE.md` — tighten `MEMORY.md` inbound rules.
- `memory/.system/COMPILE.md` — remove/reduce MEMORY.md hygiene phases and refocus.
- `memory/.system/CALIBRATE.md` — refocus on learnings/procedures.
- OpenClaw cron config — add Consolidate schedule after docs are settled.

Optional setup note to include later:

- Add a short optional modification explaining that a cron job can be designed to invoke **Claude Code / Claude CLI** through a subscription-authenticated CLI path, instead of running the work as normal OpenClaw model inference. Keep the doc intentionally high-level for now: the exact implementation pattern still needs discovery/testing. Once the pattern is figured out, remember to update the **reminders skill currently in work Clawdia** so it can use/document the same approach.

Reference plans from Claude Foreman Opus:

- Initial architecture plan: `/root/.claude/plans/analyze-the-openclaw-memory-agile-beaver.md`
- Focused Consolidate-vs-Compile separation decision: `/root/.claude/plans/think-deeply-about-one-humble-milner.md`

---

## Current Working Constraints / Guardrails

- **Consolidate must not directly edit foundation/persona/operator files** (`AGENTS.md`, `SOUL.md`, `IDENTITY.md`, `USER.md`, `TOOLS.md`). It can write recommendations into its manifest for Calibrate/human review.
- Consolidate is **preservational, not lossy**: move detail to canonical/staging files, leave pointers, preserve source detail, and avoid repeated summarization that creates telephone-game loss.
- Consolidate v1 is **`MEMORY.md` only**. Later expansion may include high-context drift surfaces like contacts/groups profiles, but only after at least 4 clean runs with no rollback/drift warnings.
- Consolidate must stay lightweight and conservative so Compile still has rich material for weekly synthesis.
- Consolidate is a **mover/indexer**, not a synthesizer: no lesson extraction, no contradiction resolution, no memory→knowledge graduation, no foundation edits, no deletion.
- Compile reads recent Consolidate manifests as Phase 0 to preserve visibility into moved material.
- Consolidate moves whole markdown sections only (`##` / `###`) and must skip mixed hot/stable sections that require splitting.
- Consolidate uses byte-equal relocation and locked pointer wording; no markdown cleanup or light rewriting.

---

## Decisions Made

1. **Separate Consolidate from Compile.**
   - Accepted Foreman decision: separation is correct.
   - Compile should not absorb routine consolidation/hygiene because that would bloat the weekly synthesis pass.

2. **Order is Collect → Curate → Consolidate → Compile → Calibrate.**
   - Compile remains the final weekly synthesis/finalization step.
   - Consolidate is a pre-Compile pressure valve for working-memory health.

3. **Consolidate is mover/indexer only.**
   - Verbatim relocation + pointer + manifest.
   - No summarization/synthesis except tiny pointer wording.
   - No lesson extraction, contradiction resolution, memory→knowledge graduation, foundation edits, or deletion.

4. **Collect should preserve raw detail. Curate gets the concise-entry rule.**
   - Avoid telephone-game loss by keeping Collect relatively raw/append-only.
   - Curate should write concise `MEMORY.md` entries and route larger/stable content elsewhere.

5. **First applied Consolidate must be dry-run/plan first.**
   - Review before committing migrations.

---

## Remaining Open Decisions

1. **Consolidate expansion scope after `MEMORY.md`:** should v2 include contacts/groups/profile drift?
   - Likely yes, but only after `MEMORY.md` consolidation proves safe through ≥4 clean runs.
   - Priority candidate: `memory/contacts/` and `memory/groups/` for profile drift.
   - Foundation files remain out of direct-edit scope.

2. **Knowledge routing details:** should Curate write directly to `knowledge/`, or stage ambiguous content in `memory/notes/`?
   - Current hypothesis: direct-to-knowledge is fine for clearly factual/procedural stable material; ambiguous content should go to `memory/notes/`.
   - Compile owns true memory→knowledge graduation.

3. **Cron timing:** Wed/Sat 5am PT is the current recommendation; confirm before adding.

4. **Model lane:** current 4C jobs are all `gpt-5.5`; Consolidate should likely start on `gpt-5.5` for consistency, with optional Claude CLI subscription path documented later.

---

## Acceptance Criteria

- [ ] `CONSOLIDATE.md` exists and defines Consolidate as mover/indexer, not synthesizer.
- [ ] `CONSOLIDATE.md` requires manifest writing, pre-state snapshot, hashes, and preservation invariant.
- [ ] `CONSOLIDATE.md` enumerates forbidden `MEMORY.md` sections and foundation-file write blocklist.
- [ ] `HOW-IT-WORKS.md` reflects the new 5C pipeline/order.
- [ ] `CURATE.md` prevents future MEMORY.md bloat by default.
- [ ] `COMPILE.md` adds Phase 0: read recent Consolidate manifests, inspect destinations, and verify hash continuity.
- [ ] `COMPILE.md` no longer treats MEMORY.md pruning as core weekly work.
- [ ] `CALIBRATE.md` is learning/procedure-focused and reviews Consolidate behavior monthly.
- [ ] `CALIBRATE.md` drains `archive/consolidate-manifests/RECOMMENDATIONS.md`.
- [ ] Cron/schedule exists for Consolidate, if approved.
- [ ] First Consolidate dry-run identifies concrete migrations out of `MEMORY.md`.
- [ ] First applied Consolidate run moves ≤100 lines and does not chase the final target in one pass.
- [ ] After first applied Consolidate pass, `MEMORY.md` is below target or has a documented blocker.

---

## Activity Log

### 2026-05-02

- JPop identified that `MEMORY.md` is getting much longer than intended and proposed a C3.5 consolidation step.
- Sean consulted Claude Foreman with Opus in read-only plan mode.
- First Foreman process was killed due to too-short wrapper timeout; second run completed successfully.
- Foreman recommended adding Consolidate as a Sonnet step 2× weekly and refocusing Compile/Calibrate.
- Progress doc created in `projects/memory-pipeline-consolidate/PROJECT_PROGRESS.md`.
- JPop raised ordering semantics: **Compile** feels like a final/last step, so Consolidate likely belongs before Compile, especially if Consolidate runs more frequently.
- JPop refined scope concerns: direct edits to `AGENTS.md`, `TOOLS.md`, `USER.md`, and `IDENTITY.md` are likely out of scope for Consolidate; Consolidate may need to include contacts/groups/profile drift, not just `MEMORY.md`; Curate/Collect concise-entry rules need care to avoid telephone-game loss.
- Sean attempted three additional Claude Foreman Opus consultations (scope/guardrails, cadence/order, routing rules). All three were blocked by Claude CLI usage limit (`You've hit your limit · resets 3:40am UTC`).
- Later, JPop asked for one more narrow Foreman Opus check specifically on Consolidate vs Compile separation. Foreman again recommended **separate**, with Consolidate as mover/indexer and Compile as synthesizer. Saved to `/root/.claude/plans/think-deeply-about-one-humble-milner.md`.
- Scheduled a one-shot follow-up for 2026-05-03 03:45 UTC to retry the multi-angle Opus Foreman consultation after reset and update this progress doc.
- Verified active 4C cron jobs in `/root/.openclaw/cron/jobs.json`: Collect, Curate, Compile, and Calibrate are all currently set to `model: gpt-5.5`.
- Cron safety note found during verification: Curate currently has `wakeMode: now`, while the local cron best-practice doc says new/safe jobs should use `wakeMode: next-heartbeat`. No config change made yet.
- Added optional setup note: future docs may describe a high-level pattern for cron-triggered Claude Code / Claude CLI subscription usage, keeping OpenClaw inference billing separate. Exact implementation still needs discovery/testing.
- Added reminder to update the work-Clawdia reminders skill once that Claude CLI cron pattern is figured out.
- JPop requested 2+ additional Opus/max-deliberation Foreman passes over the full docs/current `MEMORY.md` to identify first relocation targets and final hardening. Three were launched; two produced saved plans despite hitting Claude CLI limits at the end. Saved repo copies under `research/`. Plan updated with stricter v1: `MEMORY.md` only, byte-preserving whole-section moves, pre-state snapshots, hashes, locked pointer format, Compile Phase 0 hash/drift checks, and conservative first-run targets.
- Implementation doc pass completed after Opus routes were blocked by Claude credential/subscription limits; applied the Foreman-derived edits directly on GPT-5.5. Docs now define 5C lanes across README and pipeline docs, add `pipeline/CONSOLIDATE.md`, and rebalance Curate/Consolidate/Compile/Calibrate responsibilities. No live memory files or cron config touched.
