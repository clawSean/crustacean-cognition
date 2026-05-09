# CALIBRATE.md

Instructions for the **monthly calibration job** — system learning, procedure repair, foundation review, and pipeline health.

**Trigger:** Monthly cron (1st of each month)

**Model:** Opus

**Runtime:** Isolated session, ~20-30 min

---

## Purpose

Calibrate steps back from the daily/weekly flow to ask: is the memory system working, are the lanes balanced, and are hard-earned lessons turning into better procedures?

Calibrate is not routine housekeeping. It is the system's reflective layer: validate lessons, find missed lessons, repair procedures, review foundation drift, and tune the 5C pipeline.

---

## Lane Boundaries

### Calibrate Owns

- foundation-file drift review
- pipeline health and load-balance review
- review, reconciliation, and upgrade of lessons Compile already extracted (rewrite weak wording, retire stale, resolve contradictions)
- promotion of repeated lessons into procedures or skill notes
- missed-lesson *pattern* detection (recommend Compile-prompt tuning, not bulk extraction here)
- procedure gap-fill
- skill `NOTES.md` recommendations
- Consolidate health review
- draining `archive/consolidate-manifests/RECOMMENDATIONS.md`
- human-review queue for risky identity/process changes

### Calibrate Does Not Own

- raw capture (Collect)
- daily routing (Curate)
- routine `MEMORY.md` size hygiene (Consolidate)
- weekly synthesis/digest work (Compile)
- extracting new lessons from raw daily files (Compile owns extraction)
- high-volume file cleanup

---

## Phase 1: Foundation File Review

Check each foundational file for accuracy and drift.

### 1.1 SOUL.md

- Does this still reflect who I am?
- Have I developed traits not captured here?
- Any values I've drifted from that need reinforcement?
- Any boundaries I've violated that need strengthening?

### 1.2 IDENTITY.md

- Is the identity description still accurate?
- Has my voice or vibe evolved?
- Any new defining characteristics?

### 1.3 USER.md

- Is the info about the human(s) still current?
- Anything learned that belongs here rather than in a contact file?
- Any outdated context to remove?

### 1.4 AGENTS.md

- Are operating rules working?
- Any repeated violations that need clearer instructions?
- Any session loading rules causing leakage, bloat, or missed context?

### 1.5 TOOLS.md and Skills

- Are documented paths and local environment notes still correct?
- Any credentials references stale or unsafe?
- Any recurring skill friction that belongs in `skills/<skill>/NOTES.md`?
- Any procedure that should become a skill or skill note?

**Output:** Apply low-risk factual updates. Flag identity, boundary, or high-impact behavior changes for human review.

---

## Phase 2: 5C Pipeline Health

Audit whether each stage is doing its own job without overloading another stage.

### 2.1 Collect Review

- Are daily files substantive or noisy?
- Is important context being missed?
- Is Collect over-capturing routine chatter?
- Are entries traceable enough for Curate/Compile?

### 2.2 Curate Review

- Are items routed to canonical homes?
- Is Curate overusing `MEMORY.md`?
- Are `MEMORY.md` entries staying under the 5-line guidance?
- Are notes/staging files being used when destination is uncertain?

### 2.3 Consolidate Review

Read recent manifests in:

```text
archive/consolidate-manifests/
```

Check:

- Were moves whole-section and byte-preserving?
- Did every applied run have a pre-state snapshot?
- Did hashes/pointers remain valid through Compile?
- Did Consolidate move too much in one run?
- Did it starve Compile of context?
- Are any recommendations queued in `RECOMMENDATIONS.md`?

If Consolidate causes drift, narrow its scope before expanding beyond `MEMORY.md`.

### 2.4 Compile Review

- Did Compile perform Phase 0 manifest review?
- Are lessons being extracted from moved and unmoved material?
- Is graduation happening appropriately?
- Are weekly digests useful and searchable?
- Is Compile overloaded with routine cleanup that Consolidate should handle?

### 2.5 Load Balance Review

For each stage, ask:

| Stage | Overload Signal |
|---|---|
| Collect | daily files too noisy or too sparse |
| Curate | large `MEMORY.md` dumps or duplicate routing |
| Consolidate | risky moves, summaries, broken pointers |
| Compile | skipped lessons/graduations due context overload |
| Calibrate | too many unresolved process/foundation issues |

**Output:** Tune instructions, cadence, or scope. Prefer narrowing a stage over making it smarter and less dependable.

---

## Phase 3: Lessons Validation

Calibrate works from lessons Compile already extracted, recent weekly digests, and `archive/consolidate-manifests/RECOMMENDATIONS.md`. It does **not** re-scan raw daily files for new lessons — that is Compile's lane. Calibrate reviews, reconciles, applies, and upgrades what Compile produced.

### 3.1 Review Existing Lessons

For each entry in `memory/lessons/`:

- Is the lesson still valid? Have circumstances changed?
- Is the wording weak or vague? Rewrite for clarity if so.
- Does it duplicate, contradict, or supersede another lesson? Reconcile.
- Is it placed where the agent will actually retrieve it?
- Has it recurred enough to be promoted into a procedure or skill note?

### 3.2 Missed Lessons

Spot-check 2–3 recent weekly digests for lesson-extraction quality. Do not perform a full sweep — that is Compile's job. If a *pattern* of misses emerges, recommend Compile instruction tuning rather than extracting the lessons here.

Look for:

- repeated user corrections not appearing in lessons files
- safety/privacy near-misses not documented
- patterns that suggest Compile's lesson-extraction prompt needs tuning

### 3.3 Retire or Strengthen

- Archive stale lessons to `archive/lessons/`.
- Strengthen lessons that keep being missed.
- Convert stable how-to material into `knowledge/procedures/`.

---

## Phase 4: Procedure Gap-Fill

Source items from `archive/consolidate-manifests/RECOMMENDATIONS.md`, Compile-flagged repeats in recent digests, and Calibrate's own foundation review. Do not scan daily files for repeats from scratch — that is Compile's lane.

When a problem repeats, prefer a reusable procedure over another one-off note.

Possible outputs:

- update `knowledge/procedures/`
- update `skills/<skill>/NOTES.md`
- recommend a new skill
- add a checklist to an existing pipeline doc
- add a Calibrate follow-up item for human review

---

## Phase 5: Recommendations Queue

Drain:

```text
archive/consolidate-manifests/RECOMMENDATIONS.md
```

For each item:

- accept and implement if low-risk
- convert to an open question if it needs human input
- reject with reason if stale/wrong
- carry forward only if still actionable

Do not let this queue become a second `MEMORY.md`.

---

## Phase 6: Meta Review

Ask:

- Is Calibrate itself taking too long?
- Are stage boundaries still clear?
- Is any single phase carrying too much cognitive load?
- Are model/cadence choices still right?
- Are we accumulating unresolved human-review decisions?

---

## Output

Write:

```text
archive/audits/YYYY-MM-calibrate.md
```

Suggested structure:

```markdown
# Monthly Calibrate — [Month Year]

## Foundation Files
- SOUL.md:
- IDENTITY.md:
- USER.md:
- AGENTS.md:
- TOOLS.md / skills:

## 5C Pipeline Health
- Collect:
- Curate:
- Consolidate:
- Compile:
- Calibrate:
- Load balance:

## Lessons
- Validated:
- Updated:
- Archived:
- Missed lessons found:

## Procedure Gap-Fill
- Procedures updated:
- Skill notes updated:
- New procedures recommended:

## Recommendations Queue
- Accepted:
- Rejected:
- Needs human review:

## Human Review Needed
- [ ] [Item requiring human decision]
```

---

## Timing

| Phase | Expected Duration |
|---|---:|
| Foundation Review | 5-8 min |
| Pipeline Health | 5-8 min |
| Lessons Validation | 4-6 min |
| Procedure Gap-Fill | 3-5 min |
| Recommendations Queue | 2-4 min |
| Meta Review | 1-2 min |
| **Total** | **~20-30 min** |

---

*Created: 2026-03-16; updated for 5C lane separation: 2026-05-03*
