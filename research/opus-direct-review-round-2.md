# OPUS DIRECT REVIEW ROUND 2 — Load Balance / Context Pressure

## Verdict

**Strong overall.** Round 1's lane separation held up. The 5C now has clearly differentiated jobs, and Consolidate vs Curate is conceptually clean: Curate routes *new* raw → durable; Consolidate moves *cooled* working-memory sections → existing canonical homes. Different inputs, different outputs, different failure modes. Good.

**However, two real load-balance risks remain, and one minor conceptual smudge:**

1. **Compile is the new mega-phase.** It owns 7 phases including manifest review, file org, entity promotion, staleness pruning, lesson extraction, graduation, pattern recognition, *and* digest. Opus + 20–30 min budget masks this, but the doc itself is the longest of the five and asks for the most judgment per minute. This is exactly the failure mode JPop flagged.
2. **Calibrate has scope creep into Compile-adjacent work.** "Missed lesson detection" and "procedure gap-fill" require re-reading weeks of digests/daily incidents — that's synthesis work, not auditing. Risk: Calibrate becomes "Compile-but-monthly-and-bigger."
3. **Curate is fine but slightly under-bounded.** It can still write to `MEMORY.md`, contacts, groups, lessons, goals, reminders, ideas, lists, notes, knowledge/topics, knowledge/research, knowledge/procedures — 12 destinations with judgment per item. Not broken, but the widest router in the system.

**Curate vs Consolidate clean?** Yes. The "Curate promotes raw→durable; Consolidate moves cooled working-memory" framing is sharp and repeated consistently. Keep it.

---

## Where the Overload Actually Lives

### Compile (most concerning)

| Phase | Cognitive Type | Time |
|---|---|---|
| 0. Manifest review | Verification | 2-4 |
| 1. File organization | Mechanical | 2-3 |
| 2. Entity review/promotion | Judgment | 2-3 |
| 3. Staleness pruning | Judgment + risk | 2-3 |
| 4. Lesson extraction | High judgment | 3-5 |
| 5. Graduation | High judgment | 3-5 |
| 6. Pattern recognition | High judgment | 2-3 |
| 7. Digest | Synthesis | 2-3 |

Three high-judgment phases back-to-back (4, 5, 6) with shared input material. Phase 1 (file org) and Phase 3 (staleness pruning) are *mechanical/janitorial* and don't belong in the same job as lesson extraction. They smell like Consolidate v2 work that got left in Compile.

### Calibrate

Phase 3.2 ("Missed Lessons") asks Calibrate to scan recent digests + audits + incidents for lessons Compile missed. That's re-doing Compile's work at lower frequency. Either trust Compile or define this as "spot-check 3 random weeks," not a full sweep.

Phase 4 (Procedure Gap-Fill) is fine but should explicitly say "from items already flagged by Compile or in RECOMMENDATIONS.md," not "scan for repeats."

### Curate

Acceptable. The Promotion Heuristic + Routing Decision Tree do the load-bearing work. The 5-line `MEMORY.md` cap is the right pressure valve.

---

## Simplifications

### S1. Move Compile Phases 1 & 3 out of Compile
File organization (Phase 1) and staleness pruning (Phase 3) are mechanical. Either:
- **Option A (preferred):** Expand Consolidate v2 charter (post-4-clean-runs) to include "archive daily files >30d" and "sweep loose session-summary files." These are byte-preserving, mechanical, low-judgment — Consolidate's exact lane.
- **Option B:** Make them a tiny separate "Compile Phase 0.5: Mechanical Housekeeping" explicitly flagged as non-judgment work that can be skipped if time-pressured.

This drops Compile from 8 phases to 6 and concentrates it on judgment work.

### S2. Cap Compile's judgment phases
Phases 4–6 (lessons / graduation / patterns) share inputs. Combine into a single **"Phase 4: Synthesis Pass"** with three outputs (lessons, graduations, patterns). One read of the week's material, three deliverables. This reflects how Opus actually works — re-reading three times is wasteful and increases context pressure.

### S3. Narrow Calibrate's scope
Replace "Missed Lessons" full sweep with "Spot-check 2-3 weeks of digests for lessons that should have been caught; if pattern emerges, flag for Compile instruction tuning." Calibrate audits the *process*, not the *content*.

### S4. Add an explicit "When Compile is overloaded" escape valve
Currently Compile has no defined behavior if it runs long or context fills. Add: "If context pressure is high, prioritize Phase 0 (manifest review) and Phase 4 (synthesis); defer Phase 2 (entity promotion) to next week with note in digest." This is the load-balance failsafe.

### S5. Conceptual clarity tweak
The "Curate promotes raw→durable; Consolidate moves cooled working-memory" framing is good but appears in slightly different forms across docs. Pick one canonical sentence and use it verbatim everywhere. Suggested:

> **Curate promotes raw experience into durable memory. Consolidate relocates cooled sections out of working memory. Compile interprets and learns. Calibrate audits the system.**

---

## Exact Edit Recommendations

### COMPILE.md

1. **Move Phase 1 (File Organization)** → Either delete from COMPILE.md and add to a future Consolidate v2 charter, OR rename to **"Phase 0.5: Mechanical Housekeeping"** with header note: *"Skippable under context pressure. Candidate for migration to Consolidate v2."*

2. **Move Phase 3 (Staleness Pruning)** → Same treatment. Pruning of orphan refs and clearly-temporary 30+ day items is mechanical.

3. **Merge Phases 4, 5, 6** into **"Phase 4: Weekly Synthesis Pass"** with three subsections (4a Lessons, 4b Graduation, 4c Patterns & Contradictions). Single read of source material; three outputs. Update timing table to one row at 8-12 min.

4. **Add new section after Phase 0:** "Context Pressure Protocol"
   ```
   If context exceeds 60% during run:
   - Always complete: Phase 0 (manifest review), Phase 4 (synthesis)
   - Defer to next week with digest note: Phase 2 (entity promotion)
   - Skip with digest note: Phase 0.5 (housekeeping)
   ```

5. **Tighten Phase 2 (Entity Review):** Add a hard cap — *"Promote at most 2 entities per Compile run. Queue overflow for next week or Calibrate."* This prevents Compile from becoming a multi-entity migration job.

### CALIBRATE.md

1. **Phase 3.2 (Missed Lessons):** Rewrite from "Scan recent digests, audits, and notable incidents" → *"Spot-check 2 random weekly digests for lesson-extraction quality. If a pattern of misses emerges, recommend Compile instruction tuning rather than extracting the lessons here."*

2. **Phase 4 (Procedure Gap-Fill):** Add scoping sentence at top: *"Source items only from RECOMMENDATIONS.md, Compile-flagged repeats, and Calibrate's own foundation review. Do not scan daily files or digests for repeats — that is Compile's job."*

3. **Phase 2.5 (Load Balance Review):** This is good and should be *strengthened*. Add explicit overload signals:
   ```
   | Compile | runtime >35min, deferred phases 2+ weeks running, lessons-extracted count dropping |
   ```

### CURATE.md

1. **Add explicit anti-stuffing reinforcement** at top of `MEMORY.md` Write Rules:
   *"Default assumption: this item does NOT go in MEMORY.md. Justify inclusion against the four criteria below."*

2. **Routing Decision Tree:** No change needed. It's the cleanest doc of the five.

### CONSOLIDATE.md

1. **No changes to v1 scope.** The conservatism is correct.

2. **Add forward-looking note** at end of "V1 Scope" section:
   *"V2 candidates after 4 clean runs: archival of daily files >30 days; sweep of session-summary files in memory/ root. Both are byte-preserving and mechanical, fitting Consolidate's lane better than Compile's."*

### HOW-IT-WORKS.md & README.md

1. **Standardize the lane sentence.** Use the canonical version (S5 above) verbatim in:
   - README.md "Lane Separation" intro
   - HOW-IT-WORKS.md "Lane Separation Summary"
   - CONSOLIDATE.md Purpose
   - COMPILE.md Purpose

2. **Update Compile description** in both docs to reflect 6 phases instead of 8 once edits land.

3. **Add to Design Principles (both docs):**
   *"Mechanical work belongs with mechanical movers. Judgment work belongs with judgment models. Don't mix them in the same phase."*

---

## Bottom Line for JPop

- **Curate vs Consolidate:** clean. Ship as-is with minor anti-stuffing reinforcement.
- **Consolidate:** correctly conservative. Plan its v2 expansion to absorb Compile's mechanical phases.
- **Compile:** the current overload risk. 8 phases mixing janitorial and high-judgment work. Recommended: drop to 6 phases, merge synthesis trio, add context-pressure protocol, cap entity promotions per run.
- **Calibrate:** mild scope creep into Compile territory. Tighten "missed lessons" and "gap-fill" to audit-the-process, not redo-the-work.
- **No phase is yet a true mega-phase**, but Compile is closest. The above edits keep it honest.

The architecture is sound. The remaining risk is concentrated and fixable with surgical edits, not a redesign.
