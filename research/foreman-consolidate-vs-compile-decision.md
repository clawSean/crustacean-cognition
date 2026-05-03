# Consolidate vs. Compile — Architecture Decision

## Context

`MEMORY.md` is breaching its ~500-line working-memory/index cap. JPop proposed a new **C3.5 Consolidate** stage to keep `MEMORY.md` lean. Two competing concerns:

- **JPop:** `Compile` already feels like the *final weekly synthesis* step. Loading routine `MEMORY.md` hygiene into it risks bloat and dilutes its synthesis value.
- **Counter-concern:** A separate `Consolidate` running before/more often than `Compile` could become lossy or introduce telephone-game drift, starving `Compile` of rich source material.

This document gives a single decisive recommendation on whether the proposed Consolidate job should remain **separate from Compile** or be **folded into Compile**.

Source material reviewed (read-only):
- `README.md` — pipeline overview and design decisions
- `pipeline/COMPILE.md` — current 7-phase weekly Compile spec
- `PROJECT_PROGRESS.md` — Foreman recommendation, JPop concerns, open decisions

---

## Recommendation: **Keep Consolidate SEPARATE from Compile**

The cadence argument is decisive. The telephone-game risk is real but is a *scope design* problem, not an *architecture* problem — it is fully addressable with strict mover-not-summarizer rules below.

---

## Why separate wins

### 1. Cadence mismatch is the load-bearing argument

`MEMORY.md` is **hot-path working memory** — loaded into every main session. It bloats *throughout the week* as Curate writes new daily entries. A weekly Compile cadence means `MEMORY.md` is degraded **6 of every 7 days**. That cost is paid by every conversation, not just Sunday's pass. A weekly job structurally cannot fix a daily problem.

### 2. Compile's existing triggers are entity-shaped, not bloat-shaped

`COMPILE.md` Phase 2 promotes on "10+ lines per entity" or "5+ mentions." These catch *one fat contact section*. They do not catch the actual bloat modes seen in practice: accumulated cron references, technical insight blobs, old pass logs, drifty procedural sections. Folding hygiene into Compile without rewriting the trigger model would not solve the reported problem.

### 3. Compile's unique value is judgment, not housekeeping

Lesson extraction, graduation, contradiction detection, and pattern recognition are the phases that *require* Opus. Mechanical "move stable section, leave pointer" is Sonnet-grade work. Mixing them in one pass:
- Burns Opus budget on cheap work
- Risks the synthesis phases getting short-changed late in a 7-phase marathon
- Couples a hygiene cadence question to a synthesis cadence question that should be independent

### 4. The README's "context already loaded" defense doesn't apply here

The README defends merging mechanical and intelligent work in *Compile* because the model loads the workspace once and reuses it. That argument applies *within* a single pass. It does **not** argue against having a *different* job at a *different* cadence — Consolidate's value is precisely that it runs when Compile is not running.

### 5. Foreman already arrived at the same conclusion

Foreman's prior recommendation (Sonnet, 2× weekly, distinct step) is consistent with the cadence/cost analysis above. This is convergent evidence, not a tiebreaker.

---

## Proposed job boundary

**Core principle: Consolidate is a *mover and indexer*, not a *summarizer or synthesizer*.**

It relocates content and leaves pointers. It never compresses, interprets, graduates, or extracts. Synthesis stays with Compile and Calibrate.

### What Consolidate DOES
- Reads `MEMORY.md` and applies a size budget (target ≤350 lines, tripwire 400, hard stop 500)
- Identifies sections that are stable, cooled, or mis-located for working memory
- **Relocates verbatim** to the correct existing home:
  - long technical insight → `memory/notes/<topic>.md` (staging) or existing `knowledge/topics/<name>.md` if one already exists
  - cooled person/group context → existing `memory/contacts/` / `memory/groups/` files
  - old pass logs → `archive/`
  - cron / tooling reference → `TOOLS.md` *recommendation only* (see guardrails)
- Leaves a one-line pointer in `MEMORY.md` where useful for retrieval
- Writes a per-run **manifest** of every move: `archive/consolidate-manifests/YYYY-MM-DD-HHMM.md`

### What Consolidate MUST NOT do (so Compile keeps rich context)

| Forbidden | Why |
|---|---|
| **Summarize or rewrite** moved content | Verbatim relocation preserves source. Any compression is telephone-game loss. |
| **Extract lessons** | Compile owns lesson extraction from a full week of cooled context. |
| **Graduate `memory/` → `knowledge/`** | Requires the factual/stable/substantial/transferable judgment Compile is built for. Consolidate stays *within* memory tiers (MEMORY.md → `memory/notes/` or existing memory files; or → `archive/`). |
| **Resolve contradictions** | Cross-source judgment is Compile's Phase 6. |
| **Create new long-term files** | Only relocates into existing files or `memory/notes/` staging. New `knowledge/topics/` or `memory/contacts/` files are a synthesis act and belong to Compile. |
| **Delete content** | Always `trash`, never `rm`. Never delete content that has not yet been seen by at least one Compile cycle (protects Compile's input stream). |
| **Edit foundation files** | `AGENTS.md`, `SOUL.md`, `IDENTITY.md`, `USER.md`, `TOOLS.md` are out of write scope. Recommendations only, written into the manifest for Calibrate/human review. |
| **Touch daily files** | `memory/YYYY-MM-DD.md` is Curate/Compile territory. Consolidate's mouth is on `MEMORY.md` (and later, optionally, on contacts/groups profile drift — but only after the MEMORY.md scope is proven stable). |
| **Run any pattern recognition or backlinking** | Compile Phase 6 owns this. |

The invariant: **post-Consolidate union of `MEMORY.md` + relocations + manifest == pre-Consolidate `MEMORY.md`** (modulo whitespace/empty sections). If you can't recover the pre-state from the manifest, you violated the rule.

---

## Cadence and model

| Field | Recommendation |
|---|---|
| Cadence | **2× weekly: Wednesday + Saturday, ~5am PT** |
| Model | **Sonnet** (mechanical work; reserve Opus for Compile/Calibrate) |
| Runtime | 5–8 min isolated session |
| Order in pipeline | **Collect → Curate → Consolidate → Compile → Calibrate** (5C, but transitionally label C3.5) |

Saturday Consolidate immediately precedes Sunday Compile. This is **safe** under the no-summarize/no-delete rule: anything Consolidate relocated is still readable by Compile in its new location, often more cleanly than when it was buried in `MEMORY.md`. Compile additionally reads the most recent week of Consolidate manifests as input, so it knows what was moved and where to look.

If preservation risk later proves too high in practice, the fallback is to drop to **1× weekly Consolidate, Saturday only**, as a pre-flight cleanup before Sunday Compile. Avoid this unless data forces it — daily working-memory hygiene is the whole point.

---

## Safety guardrails

1. **Dry-run first applied run.** First Consolidate execution outputs a diff/plan only; human reviews before any move is committed. (Already in PROJECT_PROGRESS acceptance criteria.)
2. **Per-run manifest, always.** Even no-op runs write `archive/consolidate-manifests/...` with "nothing to do." Makes telephone-game risk fully auditable and reversible.
3. **Preservation invariant** (above). Any violation = bug.
4. **Bounded scope per pass:** move at most ~200 lines per run. Limits blast radius of any single mistake; multiple passes can converge on the target.
5. **Idempotency / no-op below threshold:** if `MEMORY.md` ≤ 350 lines, Consolidate exits with a no-op manifest. Don't fiddle for its own sake.
6. **Foundation-file write lock** (above): no direct edits to `AGENTS.md`, `SOUL.md`, `IDENTITY.md`, `USER.md`, `TOOLS.md`.
7. **Compile reads recent Consolidate manifests** as a Phase 0 input. Closes the loop — Compile stays the synthesis owner and can pull anything Consolidate moved back into its weekly review if needed. This is the structural defense against telephone-game drift.
8. **Tripwire alert above 400 lines.** If `MEMORY.md` is over tripwire after a Consolidate pass, log it for Calibrate — that's a signal Curate's inbound rules need tightening, not that Consolidate should get more aggressive.
9. **Calibrate reviews Consolidate behavior monthly.** Specifically: did anything moved by Consolidate get *re-promoted* to `MEMORY.md` because it was actually still hot? That's the canary for over-aggressive moves.

---

## Compile's revised scope (what it stops doing)

With Consolidate owning routine `MEMORY.md` hygiene, Compile sheds:
- Phase 2 entity promotion *from `MEMORY.md`* (Consolidate handles MEMORY.md; Compile retains promotion from daily-file accumulation patterns)
- Phase 1.2 loose session-summary sweep (move to Consolidate — it's pure hygiene)

Compile keeps and emphasizes:
- Daily-file archival (30+ days)
- Lesson extraction from the week
- Graduation `memory/` → `knowledge/`
- Pattern recognition, contradictions, backlinks
- Weekly digest
- **NEW:** read recent Consolidate manifests as input

This makes Compile materially leaner and more synthesis-focused, which is exactly JPop's stated goal.

---

## Files that would change (for downstream implementation, not this turn)

- `pipeline/CONSOLIDATE.md` — new
- `pipeline/COMPILE.md` — trim Phase 1.2 and Phase 2 MEMORY.md scope; add manifest-read step
- `pipeline/CURATE.md` — tighten MEMORY.md inbound rules (≤5 lines per new entry, prefer pointers)
- `pipeline/HOW-IT-WORKS.md` — add Consolidate to diagrams, tables, ordering
- `pipeline/CALIBRATE.md` — add Consolidate-behavior canary review
- `README.md` — update 4C → 5C diagram and tables
- OpenClaw cron config — add Consolidate (Wed/Sat 5am PT, Sonnet) after docs ship

---

## Verification (when implementation lands)

- After first applied Consolidate pass, `MEMORY.md` is below 350 lines
- A Consolidate manifest exists in `archive/consolidate-manifests/`
- Every line removed from `MEMORY.md` is locatable in the manifest's destination column
- The next Compile run reads the recent manifests and references them in the weekly digest
- One month in, Calibrate's canary check finds zero "moved-then-re-promoted" cases (or, if it finds some, Curate's inbound rules get tightened — not Consolidate's behavior loosened)

---

## Decisive answers to the asked questions

- **Separate vs. merged?** **Separate.**
- **What Consolidate must NOT do (so Compile keeps rich context):** never summarize, never extract lessons, never graduate to `knowledge/`, never resolve contradictions, never create new long-term files, never delete content not yet seen by a Compile cycle, never touch foundation files, never touch daily files. Verbatim relocation + pointer + manifest. Mover, not synthesizer.
- **If we had merged instead, how to avoid bloat?** We can't, structurally — see "cadence is decisive" above. The merged path solves a 1×/week problem with a 1×/week tool. Not recommended.
