# Skeptical KISS Review — Round 3

Overall: docs are tight and the mental model lands. Lane separation reads cleanly. Most issues are small wording snags and a couple of real guardrail gaps. No structural rewrite needed.

---

## MUST-FIX

### 1. README pipeline diagram has a wrong cadence label
In README.md "The 5C Pipeline" ASCII block:
```
🪣 COLLECT (bi-hourly)          📚 CURATE (daily)
```
is under the heading **"CONTINUOUS / DAILY"** — fine. But Collect's stage description in HOW-IT-WORKS uses an empty line where a cadence line was clearly intended ("**Trigger:**" missing for Stages 1–5). Each stage section in HOW-IT-WORKS.md has a blank line where the other docs have an explicit `**Trigger:**` line. Either add `**Trigger:** Bi-hourly cron` etc., or delete the stray blank lines. Current state looks like a templating bug.

### 2. README contradicts itself on the `archive/` location
README directory tree shows `archive/` at workspace root (sibling to `memory/` and `knowledge/`). Then the prose says:
> "archive/ can be placed inside knowledge/ (as knowledge/archive/) if you want archived content included in semantic retrieval"

But several pipeline docs hard-code `archive/consolidate-manifests/`, `archive/digests/`, `archive/audits/`, `archive/daily/` as fixed paths. If users actually move `archive/` under `knowledge/`, every cron job's manifest/digest path breaks silently.

Fix: drop the "you can move it" suggestion, OR state explicitly that the path is configurable in one place and the pipeline docs use `<archive>/...`. Pick one. Right now it's a footgun.

### 3. CONSOLIDATE tripwire logic is ambiguous
```
5. If MEMORY.md is ≤350 lines, write a no-op manifest and stop.
6. If MEMORY.md is >400 lines, treat this as a tripwire: plan conservatively…
```
What happens at **351–400 lines**? Implied "proceed normally," but never stated. Also "tripwire" isn't defined — does it block the run, force dry-run, or just add a flag? Spell out the three bands:
- ≤350: no-op
- 351–400: normal applied run allowed
- \>400: applied run allowed but flagged; consider dry-run first

### 4. CONSOLIDATE "byte-preserving" claim is technically false
> "preserve bytes exactly in the destination, except for necessary surrounding headings/provenance wrapper"

If you wrap with headings/provenance, it is not byte-preserving — it's content-preserving. The hash invariant in the manifest only works if you hash the *moved section body*, not the wrapped destination. Either:
- say "content-preserving (section body bytes unchanged; wrapper added in destination)," or
- specify that the source-body hash must match a sub-region of the destination.

This matters because Compile Phase 0 says "verify source/destination hashes" — currently undefined what's being hashed.

### 5. Locked pointer format is under-specified for Phase 0 verification
```
→ Moved to `<path>` (Consolidate YYYY-MM-DD).
```
Compile is supposed to verify the pointer still exists and find the moved material. Without a section anchor, Compile can't locate *where* in `<path>` the content lives if the destination has many sections. Add the destination heading to the pointer:
```
→ Moved to `<path>` § <Heading> (Consolidate YYYY-MM-DD, manifest <id>).
```
The manifest ID also lets Phase 0 cross-reference without date guessing.

### 6. Curate "5-line cap" vs "10-line = file" creates a dead zone
CURATE.md:
> "hard cap per curated item: 5 lines"
> "if it needs more than 10 lines, it is not a `MEMORY.md` entry; it is a file"

So 6–10 lines is… what? The 5-line line is already the hard cap. Drop the 10-line sentence; it muddies a clean rule. Or rewrite as: "5 lines max in MEMORY.md; anything longer goes in a canonical file with a pointer."

### 7. README "How tiers affect retrieval" paragraph is platform-coupled
> "Files in memory/ are automatically included in OpenClaw's native memory search on every message intake. Files in knowledge/ are searched via semantic vector search…"

README also says "the architecture is platform-agnostic." Pick one framing. If the tier behavior depends on OpenClaw, say "On OpenClaw, …" so adopters on other platforms know they have to wire this themselves.

---

## NICE-TO-HAVE

### 8. "Sonnet+" is undefined
CONSOLIDATE uses "Sonnet+" and "Sonnet or stronger." HOW-IT-WORKS uses both. Pick one phrase and define it once (e.g., "Sonnet 4.5 or higher").

### 9. README's "C is for Crustacean" appears twice
Top of README and bottom. The bottom one with the 5C bullets is the punchier one; the opening tagline can lose the explanation and just say "the 5C cognitive architecture."

### 10. "Curate is not the opposite of Curate" sentence reads awkwardly
README:
> "Consolidate is not the opposite of Curate. Curate promotes raw experience into durable memory; Consolidate keeps the working-memory index from becoming a warehouse."

Tighten: "Curate promotes new material *in*; Consolidate moves cooled material *out of `MEMORY.md`*. They aren't opposites — they manage different flows."

### 11. CALIBRATE Phase 5 contradicts CONSOLIDATE's recommendations sink
CONSOLIDATE writes recommendations to `archive/consolidate-manifests/RECOMMENDATIONS.md`. CALIBRATE drains it. Fine. But CONSOLIDATE also says "Append recommendations that affect foundation files, pipeline policy, or human decisions" — implying *only those* go to RECOMMENDATIONS.md. Other recs presumably stay in per-run manifests. CALIBRATE doesn't say it reads per-run manifests for recs, only the aggregate file. Either:
- have Consolidate dump *all* recs to the aggregate file, or
- have Calibrate explicitly read both.

### 12. Compile Phase 1.2 (session-memory hook sweep) is platform-specific trivia in a generic doc
Same issue as #7. If `session-memory` is OpenClaw-specific, mark it. Generic adopters will read this and be confused.

### 13. README "Getting Started" step 2 path
> "Copy the pipeline docs from pipeline/ to your memory/.system/."

Why the rename? If both are valid paths, the docs cross-referencing each other (e.g., "see memory/.system/COLLECT.md") are wrong half the time. Just use one canonical location in instructions and explain the dual layout (repo vs. deployed) once.

### 14. "First applied run ≤100 lines" — first ever, or first per cycle?
CONSOLIDATE says "first applied run: move at most 100 lines." Forever, or just the first one after deployment? Read it three times and still ambiguous. Suggest: "First applied run after deployment (or after any rule-set change)."

### 15. Compile Phase 2.1 thresholds overlap with Consolidate
> "10+ lines in MEMORY.md" as a promotion candidate.

Consolidate already moves cooled whole sections. If a 10+ line entity is in MEMORY.md, Consolidate may have already moved it. Add a one-liner: "Skip entities already relocated by Consolidate this cycle (see Phase 0)."

### 16. HOW-IT-WORKS decision tree omits the working-memory check ordering
The tree asks "Is it a personal experience?" before "Is it immediately relevant?" That's correct, but means a hot personal item should land in a contact file *and* maybe MEMORY.md as a pointer. The tree doesn't acknowledge dual-write. One sentence: "Items can have a canonical home AND a MEMORY.md pointer when currently hot."

### 17. No guardrail against MEMORY.md re-bloat after Consolidate
Nothing prevents Curate from re-stuffing MEMORY.md the day after Consolidate trims it. CURATE.md has the 5-line rule per item but no aggregate budget. Consider: "If MEMORY.md is already >450 lines, prefer canonical-home + pointer over any new MEMORY.md entry." Cheap, keeps the system stable.

### 18. "Crustacean" framing fights itself
Lobsters molt = shed and grow new. The system *doesn't* shed — it archives, manifests, and preserves. The metaphor is cute but slightly off. Minor; ignore if you like the vibe.

---

## Summary

Mental model is intact and clear across all five docs. Real risks are: (a) `archive/` path inconsistency, (b) under-specified Consolidate hash/pointer mechanics that Compile Phase 0 depends on, and (c) a few platform-specific assumptions leaking into "platform-agnostic" framing. Fix those four or five things and this is shippable.
