# COMPILE.md

Instructions for the **weekly compilation job** — synthesis, finalization, lesson extraction, graduation, and pattern recognition.

**Trigger:** Weekly cron (Sunday)

**Model:** Opus

**Runtime:** Isolated session, ~20-30 min

---

## Purpose

Compile is the **weekly synthesis and lesson factory** of the memory system. It turns accumulated experience into durable learning: lessons, graduations, pattern notes, contradiction fixes, backlinks, and weekly digest.

Compile is **not** the janitor. Routine `MEMORY.md` size hygiene belongs to Consolidate; mechanical age/size-based archival is a Consolidate v2 candidate. Compile owns the judgment-heavy synthesis that Consolidate must not attempt.

> **Curate routes; Compile synthesizes. Consolidate moves; Compile decides.**

Opus is used because lessons, graduation, contradiction resolution, and pattern synthesis require judgment to avoid premature or lossy changes.

---

## Lane Boundaries

### Compile Owns

- reading recent daily files and Consolidate manifests
- validating that moved material remains visible
- lesson extraction from experiences
- graduation from memory → knowledge when mature
- contradiction detection and resolution
- cross-file pattern recognition and backlinks
- weekly digest creation
- backstopping stale or risky Consolidate behavior

### Compile Does Not Own

- daily routing of fresh raw notes (Curate)
- acting as daily router or entity-to-canonical-home mover (Curate)
- routine `MEMORY.md` line-count management (Consolidate)
- monthly foundation-file/process redesign (Calibrate)
- blind pruning without provenance

---

## Phase 0: Consolidate Manifest Review

This phase is mandatory whenever Consolidate exists.

### 0.1 Read Recent Manifests

Read all manifests in:

```text
archive/consolidate-manifests/
```

since the last Compile digest.

For each manifest:

- inspect moved source headings
- inspect destinations
- verify source/destination hashes when provided
- confirm the pointer still exists in `MEMORY.md`
- note any skipped/flagged sections
- collect material marked for Compile synthesis

### 0.2 Preserve Synthesis Context

Moved material is still part of the week's memory stream. Include it when looking for:

- lessons
- graduation candidates
- contradictions
- recurring patterns
- digest-worthy events

If moved material is hard to find, fragmented, or under-described by the pointer, flag Consolidate drift in the digest and for Calibrate.

### 0.3 Failure Modes

If hashes do not match, destinations are missing, or pointers are broken:

1. stop additional pruning/promotions involving that material
2. restore from the pre-state snapshot if necessary
3. record the issue in the weekly digest
4. flag for Calibrate/human review

---

## Context Pressure Protocol

The synthesis core — manifest review, lesson extraction, graduation, and contradiction/pattern recognition — must be protected. Mechanical/janitorial work is first to drop.

If context fills during the run, prioritize in this order:

1. Phase 0 (manifest review) — always complete
2. Phase 4 (lesson extraction) — always complete
3. Phase 5 (graduation) — complete if context allows
4. Phase 6 (contradictions, patterns) — complete if context allows
5. Phase 2 (entity review) — defer with a digest note
6. Phase 1 and Phase 3 (mechanical/staleness sweeps) — skip with a digest note; flag candidates for Consolidate v2

---

## Phase 1: Archival Sweeps Requiring Judgment

Compile performs only the archival moves that require interpreting *what* content is. Mechanical age-based or size-based hygiene belongs to Consolidate; it will expand to cover daily-file archival in v2. **All Phase 1 sweeps are skippable under context pressure** — protect synthesis instead, and flag the deferred sweeps as Consolidate v2 candidates in the digest.

### 1.1 Archive Old Daily Files

Move daily files older than 30 days:

```text
memory/daily/2026-01-*.md → archive/daily/2026-01/
```

### 1.2 Sweep Loose Session Summaries

Move any dated `YYYY-MM-DD*.md` files from `memory/` root into `archive/daily/`.
These are session summaries created by the `session-memory` hook when `/new` is issued. Their content is redundant with what Collect already extracts from full session transcripts.

### 1.3 Clean Up

- Remove empty files only when clearly safe.
- Remove exact duplicate files only after verifying content identity.
- Use `trash` instead of `rm`.
- Log file operations in the weekly digest.

---

## Phase 2: Synthesis-Oriented Entity Review

Compile may promote overgrown entities when judgment is needed, but should not do routine line-count cleanup that Consolidate can safely handle. **Cap entity promotions at 2-3 per run** — defer the rest to next week or flag for Calibrate.

### 2.1 Check Candidates

Scan for entities with 5+ mentions across *multiple weeks* of daily files/manifests, where cross-week synthesis is required to form one coherent canonical file. Pure line-count overgrowth in `MEMORY.md` is Consolidate's lane, not Compile's. Single-week line moves are not a Compile concern.

Skip entities already relocated by Consolidate this cycle (confirmed via Phase 0 manifests).

### 2.2 Promotion Routing

| Entity Type | Destination |
|---|---|
| Person | `memory/contacts/<channel>-<id>.md` |
| Group | `memory/groups/<channel>-g-<name>.md` |
| Named entity or subject | `knowledge/topics/<name>.md` |

### 2.3 Promotion Steps

1. Confirm the content is stable enough to promote.
2. Create/update the canonical file.
3. Move or synthesize content as appropriate.
4. Leave a pointer in `MEMORY.md` if future retrieval benefits.
5. Update the Memory Structure Map if a new file is created.
6. Record the decision in the digest.

---

## Phase 3: Semantic Staleness Review

Archive content that is stale in *meaning* — superseded, contradicted, or already graduated. Time-based and size-based pruning belong to Consolidate. If a section is merely cold, leave it for Consolidate.

### 3.1 Safe to Archive/Purge

- 30+ days old and explicitly temporary/transient
- completed reminders already captured elsewhere
- superseded by newer entries
- orphaned references to missing files
- content already graduated and safely represented in knowledge

### 3.2 Leave Alone When Uncertain

If relevance is unclear, do not prune. Flag it for Calibrate or a future Compile.

### 3.3 Safety

- Use `trash`, not `rm`.
- Prefer archive/pointer over deletion.
- Log what changed in the weekly digest.

---

## Phase 4: Lesson Extraction

Scan the week's daily files, contact/group changes, notes, and Consolidate destinations for implicit lessons.

### 4.1 Look For

- mistakes and corrections ("turns out...", "actually...", "I was wrong about...")
- friction and frustration ("this kept failing", "had to work around...")
- successful patterns ("this worked well", "the trick is...")
- surprises ("didn't expect...", "interesting that...")
- repeated human corrections

### 4.2 Extract Format

```markdown
## Lesson Title
**Learned:** [date] (source reference)
**Context:** What happened
**Rule:** What to do / not do
```

### 4.3 Route to `memory/lessons/`

Add to `memory/lessons/lessons.md` or the most specific domain file if lessons have been split.

Check for duplicate lessons. Update existing entries rather than duplicating.

---

## Phase 5: Graduation Assessment

Identify content ready to move from episodic memory to semantic knowledge.

### 5.1 Graduation Criteria

Content is ready when all are true:

| Criterion | Question |
|---|---|
| Factual | Is it "how X works" rather than "what we did with X"? |
| Stable | Has it been unchanged for 2+ weeks? |
| Substantial | 5+ facts or 5+ references? |
| Transferable | Would it help someone without relationship context? |

### 5.2 Scan Locations

- `memory/notes/`
- `memory/lessons/` for procedures in disguise
- `MEMORY.md` pointers and hot technical sections
- Consolidate destinations from Phase 0
- recurring content in daily files

### 5.3 Graduate Process

1. Extract factual content.
2. Rewrite without temporal/personal framing.
3. Create/update `knowledge/topics/`, `knowledge/procedures/`, or `knowledge/research/`.
4. Update source with a pointer or trim safely.
5. Update `MEMORY.md` structure map if a new durable file was created.
6. Record the graduation in the digest.

### 5.4 What Stays in Memory

Never graduate:

- relationship context
- group dynamics
- personal opinions/preferences
- temporal current-status context
- inside jokes/personality unless converted into a factual cultural note

---

## Phase 6: Pattern Recognition & Cross-Referencing

### 6.1 Recurring Topics

Same subject mentioned 3+ times this week? Consider:

- creating or updating a dedicated knowledge file
- noting the pattern in the digest
- recommending a future project/procedure if repeated friction appears

### 6.2 Contradictions

Cross-reference for inconsistencies:

- `MEMORY.md` says X, but a canonical file says Y
- older daily file contradicts newer information
- lessons conflict
- Consolidate pointer no longer matches destination reality

Resolution: fresher and more specific sources usually win. Document non-obvious decisions.

### 6.3 Backlinks

Add brief references only when the connection will materially improve future retrieval.

---

## Phase 7: Weekly Digest

Create:

```text
archive/digests/YYYY-MM-DD-weekly.md
```

Suggested structure:

```markdown
# Weekly Digest — [Date Range]

## Activity Summary
- Daily files processed:
- Consolidate manifests reviewed:
- People/groups active:

## File Operations
- Files archived:
- Entities promoted:
- Content pruned/archived:

## Consolidate Review
- Manifests OK:
- Hash/pointer issues:
- Moved material synthesized:

## Lessons Extracted
- [New lesson] → memory/lessons/

## Content Graduated
- [Item] moved from [source] → [destination]

## Contradictions Resolved
- [What was inconsistent] → [resolution]

## Patterns Noted
- [Emerging pattern worth watching]

## Flagged for Calibrate
- [Items needing monthly review]

---
*Compiled: [timestamp]*
```

---

## Timing

| Phase | Expected Duration |
|---|---:|
| Consolidate Manifest Review | 2-4 min |
| File Organization | 2-3 min |
| Entity Review | 2-3 min |
| Staleness Pruning | 2-3 min |
| Lesson Extraction | 3-5 min |
| Graduation Assessment | 3-5 min |
| Pattern Recognition & Backlinks | 2-3 min |
| Weekly Digest | 2-3 min |
| **Total** | **~20-30 min** |

---

*Created: 2026-03-16; updated for 5C lane separation: 2026-05-03*
