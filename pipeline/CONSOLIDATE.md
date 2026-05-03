# CONSOLIDATE.md

Instructions for the **working-memory consolidation job** — conservative `MEMORY.md` hygiene that preserves context for Compile.

**Trigger:** Weekly or semiweekly cron, before Curate on days both run

**Model:** Sonnet or stronger for v1; upgrade if manifests show ambiguity

**Runtime:** Isolated session, ~10-15 min

---

## Purpose

Consolidate keeps `MEMORY.md` acting like working memory plus an index, not a warehouse.

It does **not** reverse Curate. Curate promotes raw experience into durable memory. Consolidate preserves that durable memory while moving cooled or over-detailed material out of the working-memory index.

> **Core rule:** Consolidate moves; Compile decides.

If a proposed move requires deciding what content *means*, whether it is a lesson, whether it is transferable knowledge, or how it should be split, skip it and flag it for Compile or Calibrate.

---

## Lane Boundaries

### Consolidate Owns

- `MEMORY.md` size hygiene
- content-preserving relocation of whole cooled sections (section body unchanged; a minimal heading or provenance wrapper may be added at destination)
- pointer maintenance back to relocated material
- relocation manifests and recovery snapshots
- recommendations for later human/Calibrate review

### Consolidate Does Not Own

- lesson extraction
- contradiction resolution
- memory → knowledge graduation decisions
- contact/group/profile pruning
- foundation-file edits (`SOUL.md`, `IDENTITY.md`, `USER.md`, `AGENTS.md`, `TOOLS.md`)
- rewriting, summarizing, or improving prose
- deletion
- directory invention

---

## V1 Scope

V1 is **`MEMORY.md` only**.

Do not compact contacts, group files, lessons, goals, reminders, notes, or knowledge files until `MEMORY.md` consolidation has completed at least **4 clean applied runs** with:

*V2 candidates (after 4 clean runs): archival of daily files >30 days old; sweep of loose session-summary files in `memory/` root. Both are mechanical and byte-preserving — better suited to Consolidate than Compile.*

- no rollback needed
- no hash/drift warnings
- no Compile reports that moved material became invisible
- no human complaint that context got harder to find

---

## Safety Invariants

Every applied run must preserve the ability to reconstruct the pre-run state from:

1. the current `MEMORY.md`
2. relocation destinations
3. the run manifest
4. the pre-state snapshot

Never make a move unless this invariant is true.

---

## Allowed Moves

A candidate is eligible only when all are true:

- it is a whole markdown section (`##` or `###`), not arbitrary lines
- the section is cooled, stable, or over-detailed for working memory
- the destination already exists or is an obvious existing canonical home
- the move does not require deciding that memory should graduate to `knowledge/` — that is Compile's lane
- the section body can be relocated without content changes
- a short pointer is enough for future retrieval
- Compile can still find and synthesize the moved material through the manifest

Good first candidates:

1. old pass-log blocks after the newest 2-5 entries
2. whole technical/reference sections that already behave like knowledge staging
3. inline entity details already fully represented in their canonical file

---

## Forbidden Moves

Never move these in v1:

- `## 📂 Memory Structure Map`
- `## 🔥 Hot / Time-Sensitive`
- `## 🔐 Security Rules`
- `## 🏷️ Trust Tiers`
- `## 💬 Communication Guidelines`
- `## 📋 Active Cron Jobs`
- `## 🦞 Identity & Setup`
- any line containing `**Firm rule:**` or `**Rule:**`
- mixed hot/stable sections that require splitting
- anything whose destination is unclear
- anything that would require summarization to remain useful

---

## Run Procedure

### 0. Preflight

1. Read `MEMORY.md`.
2. Count lines.
3. Read recent Consolidate manifests, if any.
4. Confirm destination files exist before planning moves.
5. Determine the run band:
   - **≤350 lines**: write a no-op manifest and stop.
   - **351–400 lines**: normal applied run allowed.
   - **>400 lines**: tripwire — applied run allowed but the overage must be flagged in the manifest and an explanation added to RECOMMENDATIONS.md. Consider doing dry-run first.

### 1. Dry Run

The first run for any new rule set must be dry-run only.

Output:

- proposed source section
- proposed destination
- reason the section is eligible
- estimated line reduction
- risks or open questions

Do not edit files during a dry run.

### 2. Snapshot

Before any applied run, write:

```text
archive/consolidate-manifests/YYYY-MM-DD-HHMM-pre.md
```

The snapshot should be an exact copy of pre-run `MEMORY.md`.

### 3. Move Conservatively

- first applied run: move at most **100 lines**
- normal applied run: move at most **150 lines**
- move whole sections only
- preserve the section body content exactly; a minimal heading or provenance wrapper may be added in the destination. Source-body hash must match the destination body hash (excluding wrapper).
- do not split sections
- do not summarize
- do not rewrite markdown

### 4. Leave Locked Pointers

Replace moved content with this exact pointer shape:

```markdown
→ Moved to `<path>` § <Destination Heading> (Consolidate YYYY-MM-DD, manifest <HHMM>).
```

Always include the destination heading so Compile Phase 0 can locate the section without guessing. Include a copied list of subsection titles below the pointer when the moved section contains multiple subsections. Do not write a prose summary.

### 5. Write Manifest

Every applied or no-op run writes:

```text
archive/consolidate-manifests/YYYY-MM-DD-HHMM.md
```

Manifest fields:

```markdown
# Consolidate Manifest — YYYY-MM-DD HH:MM

## Run Type
- dry-run | applied | no-op

## MEMORY.md State
- lines before:
- lines after:
- tripwire exceeded: yes/no

## Moves
*(Hashes are of the section body content only, excluding any wrapper heading or provenance lines added in destination.)*
| Source heading | Destination | Lines | Source body hash | Destination body hash | Pointer |
|---|---|---:|---|---|---|

## Skipped / Flagged
- [section] — [reason]

## Compile Phase 0 Notes
- destinations Compile must inspect
- any drift risks
- any material that may require graduation/lesson extraction

## Recommendations for Calibrate/Human
- [item]
```

Append recommendations that affect foundation files, pipeline policy, or human decisions to:

```text
archive/consolidate-manifests/RECOMMENDATIONS.md
```

---

## Compile Dependency

Compile must begin with **Phase 0: Consolidate Manifest Review**.

Consolidate is safe only if Compile reads recent manifests, inspects destinations, verifies source/destination hashes, and includes moved material in weekly synthesis.

If Compile cannot perform Phase 0, Consolidate should run dry-only or no-op.

---

## Output

At the end of a run, report:

- line count before/after
- moved sections
- manifest path
- snapshot path if applied
- skipped/flagged sections
- whether Compile Phase 0 has anything urgent to inspect

---

*Created: 2026-05-03*
