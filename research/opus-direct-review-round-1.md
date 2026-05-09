# OPUS DIRECT REVIEW — Round 1: Lane Clarity

## Verdict

**Pass with must-fix edits.** The five lanes are *conceptually* distinct and the doc has clearly been hardened against the obvious overlaps (Curate↔Consolidate, Consolidate↔Compile, Compile↔Calibrate). However, three real overlaps remain that will erode lane clarity in practice, and one phase (Compile) is still carrying meaningfully more work than the others. None require a redesign — all are fixable with targeted wording.

Ranked from most to least important:

1. **Compile Phase 1–3 (file org, entity promotion, staleness pruning) overlaps Consolidate and Curate.**
2. **Curate is allowed to "promote overgrown entities" (Phase 2 of Compile says the same thing) — two lanes claim entity promotion.**
3. **"Graduation" is owned by Compile, but Curate's routing table sends factual material directly to `knowledge/`** — so graduation happens in two places under two different names.
4. Minor: Calibrate Phase 1.5 ("any procedure that should become a skill") brushes against Compile's graduation lane.

---

## Must-Fix Edits

### Fix 1 — Strip routine cleanup out of Compile (biggest lane bleed)

**Problem.** COMPILE.md Phases 1, 2, and 3 (File Organization, Entity Review, Staleness Pruning) are exactly the kind of routine, mechanical, line-count-driven work the README and lane table explicitly assign to Consolidate ("Compile must not become a routine janitor"). Compile's own lane summary says it does not own routine `MEMORY.md` size hygiene — but Phase 2.1's trigger is literally *"10+ lines in MEMORY.md."* That is line-count hygiene.

This is the single biggest threat to lane clarity. Compile is currently: manifest review + janitor + entity promoter + pruner + lesson extractor + graduator + pattern finder + digester. That is at least three lanes' worth.

**Edit.** Reframe Phases 1–3 of Compile around *judgment*, and push mechanical work to Consolidate (or explicitly to Compile's "only when judgment is required" carve-out).

In `pipeline/COMPILE.md`, replace the opening of Phase 1:

> **Current:** "Phase 1: File Organization — Keep the workspace tidy without taking over Consolidate's lane."
>
> **Replace with:** "Phase 1: Archival Sweeps Requiring Judgment — Compile performs only the archival moves that require interpreting *what* content is, not how long it is. Pure line-count or age-based hygiene belongs to Consolidate."

In Phase 2.1, replace the trigger list:

> **Current:** "10+ lines in `MEMORY.md` · 5+ mentions across recent daily files/manifests · repeated detail spread across multiple files"
>
> **Replace with:** "5+ mentions across recent daily files/manifests, **and** detail spread across multiple files in a way that requires interpretive merging. Pure line-count overgrowth in `MEMORY.md` is Consolidate's job; Compile only intervenes when promotion requires judgment about what the entity *is*."

In Phase 3, retitle and rescope:

> **Current:** "Phase 3: Staleness Pruning"
>
> **Replace with:** "Phase 3: Semantic Staleness Review — Archive content that is stale *in meaning* (superseded, contradicted, graduated). Time-based and size-based pruning belong to Consolidate. If a section is merely cold, Consolidate moves it; Compile only acts when staleness is a judgment call."

### Fix 2 — Settle entity promotion in *one* lane

**Problem.** Curate's routing table already sends per-person content to `memory/contacts/<...>` and per-group content to `memory/groups/<...>`. Compile Phase 2.2 does the same routing for "overgrown" entities. Two lanes, same operation. The README's lane table says Curate owns "daily routing to canonical homes" and Compile owns "lessons, graduation, patterns, digest" — entity promotion is not in either column cleanly.

**Edit.** Make the rule: *Curate creates and grows canonical entity files from day one. Compile only intervenes when promotion requires synthesis across many sources Curate could not see in a 2-3 day window.*

In `pipeline/COMPILE.md` Phase 2, prepend:

> "Curate already routes per-person and per-group material to canonical homes daily. Compile intervenes here **only** when an entity has accumulated across multiple weeks in a way Curate's 2-3 day window cannot see, and a single coherent canonical file requires cross-week synthesis. If the work is just 'move these lines to the contact file,' that is Curate's lane, not Compile's."

In `pipeline/CURATE.md` under "Curate Owns," add one bullet:

> "- creating new canonical contact/group/topic files the first time durable material about a new entity appears"

### Fix 3 — Name the graduation split, or eliminate it

**Problem.** Curate's routing table sends factual material directly to `knowledge/topics/`, `knowledge/research/`, `knowledge/procedures/`. COMPILE.md Phase 5 ("Graduation Assessment") is *also* about moving content into `knowledge/`. The README claims "Content graduates from memory to knowledge when it becomes stable, factual, and transferable" — implying graduation is a maturity event, not a daily routing event. But Curate is allowed to write to `knowledge/` on day one.

This is the subtlest overlap and will cause the most confusion in practice ("did Curate already put this in knowledge/, or am I supposed to graduate it?").

**Edit — Option A (cleaner; recommended).** Forbid Curate from writing to `knowledge/` directly. Curate routes factual-looking material to `memory/notes/` or a dedicated `memory/knowledge-staging/`; Compile graduates.

In `pipeline/CURATE.md` Routing Decision Tree, change the three `knowledge/*` rows:

> **Current:**
> | Factual reference about a subject? | `knowledge/topics/` | ... |
> | A deep dive or analysis? | `knowledge/research/` | ... |
> | A how-to, workflow, or process? | `knowledge/procedures/` | ... |
>
> **Replace with:**
> | Factual reference, deep dive, or how-to? | `memory/notes/` (staged for Compile to graduate to `knowledge/`) | Curate does not write to `knowledge/` directly; graduation requires maturity judgment |

And add to "Curate Does Not Own":

> "- writing to `knowledge/` directly (graduation is Compile's lane)"

**Edit — Option B (lighter touch).** Keep Curate able to write to `knowledge/`, but rename Compile Phase 5 to "**Re-graduation and Mature Content Review**" and define the split explicitly:

> "Curate may route obviously stable factual material directly to `knowledge/` on day one. Compile's graduation lane is for material that started in `memory/` and has *matured* — that is, content Curate could not yet judge as transferable. If Curate already placed something in `knowledge/`, Compile only edits it for contradiction or pattern integration, never re-graduates it."

Pick one. Currently the docs are written as if Option B is intended but never say so, which is the source of the ambiguity. **My recommendation is Option A** — it makes the lane crisp and matches the architecture's stated philosophy ("Conservative movers, smart synthesizers"). Curate is fast and conservative; graduation is a judgment call that deserves Opus.

### Fix 4 — Tighten Calibrate's brush against Compile

**Problem.** Calibrate Phase 1.5 says: "Any procedure that should become a skill or skill note?" and Phase 4 ("Procedure Gap-Fill") allows updating `knowledge/procedures/`. Compile Phase 5 graduates how-to material into `knowledge/procedures/`. Two lanes can write the same file for similar reasons.

**Edit.** In `pipeline/CALIBRATE.md` Phase 4, prepend:

> "Calibrate writes new procedures only when a *gap* has gone unaddressed across multiple Compile cycles. Routine graduation of mature how-to material is Compile's lane. If Compile would have caught it next Sunday, leave it for Compile."

---

## Smaller Wording Changes

These don't change behavior but sharpen lane identity:

- **README lane table** — change Compile's "Must Not Become" from "routine janitor" to **"routine janitor or daily router."** This explicitly excludes both the Consolidate overlap and the Curate overlap.

- **README "Lane Separation" table, Compile row** — change "Owns" from "lessons, graduation, patterns, digest" to **"lessons, graduation, contradiction resolution, patterns, weekly digest."** Currently "contradiction resolution" appears in COMPILE.md Phase 6 but is missing from the README's lane summary, which makes Compile's ownership look smaller than it is.

- **HOW-IT-WORKS.md "Lane Separation Summary" table, Curate row** — change Output from "canonical memory/knowledge files + small hot pointers" to **"canonical memory files + small hot pointers"** (consistent with Fix 3 Option A) or **"canonical memory files, staged knowledge candidates, + small hot pointers"** (consistent with Option B).

- **CONSOLIDATE.md "Core rule"** is "Consolidate moves; Compile decides." **Add a parallel rule to CURATE.md**: "Curate routes; Compile synthesizes." Three-word rules at the top of each doc make lanes memorable.

- **CURATE.md "Lane Boundaries → Curate Does Not Own"** currently lists "memory → knowledge graduation after maturity (Compile)." If you adopt Fix 3 Option A, change to: **"writing to `knowledge/` (Compile graduates)."** Removes the "after maturity" weasel-word that currently lets Curate rationalize day-one knowledge writes.

---

## What's Already Good (Don't Touch)

- The Collect lane is genuinely clean. "Append only — never edits, reorganizes, or routes" is exactly the right level of restriction for a Haiku-class job.
- Consolidate's V1 scope ("`MEMORY.md` only · whole-section · byte-preserving · 4 clean runs before expanding") is excellent risk-management and gives the lane a sharp edge.
- "Consolidate moves; Compile decides" is the load-bearing principle of the whole architecture and it's stated cleanly in three places.
- Compile Phase 0 (manifest review) is the right dependency direction and is well-specified.
- Calibrate's "Calibrate Does Not Own" list is the strongest of the five — it explicitly disclaims raw capture, daily routing, weekly synthesis, and routine hygiene. Use it as the template for tightening the others.

---

## Difficulty / Context Balance

After Fix 1 lands, load distribution looks roughly balanced:

| Phase | Decisions per run | Files touched | Cognitive load |
|---|---|---|---|
| Collect | trivial (extract or skip) | 1 (today's daily) | low — correct for Haiku |
| Curate | moderate (route table) | ~5–15 | medium — correct for Sonnet |
| Consolidate | mechanical (pattern match against allow/forbid lists) | 1 + manifest | low-medium — correct for Sonnet+ |
| Compile | heavy synthesis (lessons, graduation, contradictions, patterns) | many | high — correct for Opus |
| Calibrate | meta-judgment + foundation edits | many | high — correct for Opus |

Without Fix 1, Compile is doing two jobs (routine hygiene + synthesis) and its 20–30 min budget is optimistic. With Fix 1, Compile becomes a pure synthesis job and the time budget tightens honestly.

---

## One-line summary for JPop

**Compile is doing too many small jobs; Curate and Compile both touch `knowledge/`; entity promotion lives in two lanes. Three targeted edits fix all three and the architecture is clean.**
