# Architecture Review C — Implementation-Spec Lens: Introducing Consolidate

## Context

`MEMORY.md` is exceeding its ~500-line working-memory cap (currently ~520 lines in JPop's workspace) because the 4C pipeline has producers (Collect, Curate) but no dedicated eviction owner. Compile is a weekly Opus synthesis pass — using it for routine MEMORY.md hygiene is the wrong tool, and once-a-week is too slow to keep working memory healthy on the other six days.

The accepted architectural decision (`PROJECT_PROGRESS.md`, two Foreman Opus passes) is to add **Consolidate (C3.5)** as a separate semi-weekly mover/indexer, slimming Compile and refocusing Calibrate on learning. This document specifies the concrete file edits.

Order: **Collect → Curate → Consolidate → Compile → Calibrate** (5C).

Hard guardrails:
- Consolidate is a **mover/indexer**, not a synthesizer. Verbatim relocation + pointer + manifest. No lesson extraction, contradiction resolution, memory→knowledge graduation, foundation edits, or deletion.
- Foundation/persona/operator files (`AGENTS.md`, `SOUL.md`, `IDENTITY.md`, `USER.md`, `TOOLS.md`) are **never directly edited** by Consolidate — recommendations only, surfaced via manifest for Calibrate/human review.
- Preservation invariant: pre-Consolidate content must be recoverable from `MEMORY.md` + relocation destinations + manifest.
- Compile reads recent manifests as Phase 0 input → preserved detail flows into weekly synthesis (avoids telephone-game loss).

Critical files to be modified (all in `/root/projects/clawSean/crustacean-cognition/`):
- `pipeline/CONSOLIDATE.md` — NEW
- `pipeline/CURATE.md` — tighten MEMORY.md inbound rules
- `pipeline/COMPILE.md` — add Phase 0, drop MEMORY.md hygiene, refocus on synthesis
- `pipeline/CALIBRATE.md` — add Consolidate health review + manifest recommendations processing; refocus on lessons/procedures
- `pipeline/HOW-IT-WORKS.md` — diagram + table + section updates for 5C
- `README.md` — diagram + table updates for 5C
- (Cron config, downstream) `/root/.openclaw/cron/jobs.json` — add Consolidate; while there, fix Curate `wakeMode: now` → `next-heartbeat`

---

## 1. NEW: `pipeline/CONSOLIDATE.md`

### Header

```
# CONSOLIDATE.md

Instructions for the **semi-weekly consolidation job** — keeping MEMORY.md
lean by relocating cooled/stable content to its canonical home, verbatim,
with pointers and a manifest.

**Trigger:** Wed + Sat ~05:00 PT (cron)
**Model:** gpt-5.5 (initially; later candidate Sonnet via Claude CLI subscription path)
**Runtime:** Isolated session, ~5–8 min
```

### Purpose

Consolidate is the **pressure valve** between Curate (daily inbound) and Compile (weekly synthesis). It owns one job: keep `MEMORY.md` healthy as working-memory + index. It moves stable content out, leaves a 1-line pointer behind, and writes a manifest so Compile and Calibrate retain visibility into what moved.

Consolidate is **not** a synthesizer. It does no lesson extraction, no contradiction resolution, no memory→knowledge graduation reasoning, no foundation-file edits, and no deletion. Those are Compile's and Calibrate's jobs.

### Targets & Tripwires

| Metric | Threshold | Behavior |
|---|---|---|
| MEMORY.md soft target | ≤ **350 lines** | If at/below, write a no-op manifest and exit |
| Tripwire | **400 lines** | Run normally; flag in manifest |
| Hard cap | **500 lines** | Run normally; flag in manifest **AND** add `severity: HIGH` so Compile/Calibrate prioritize |
| Per-run move budget | **≤ 200 lines** | Stop after budget; remainder picked up next run |

### Phases

**Phase 0 — Preflight (30s)**
1. Read `MEMORY.md`. Count lines.
2. If ≤ 350: write a no-op manifest at `archive/consolidate-manifests/YYYY-MM-DD-HHMM-noop.md` and exit.
3. Otherwise continue.

**Phase 1 — Identify Candidates (1–2 min)**
Scan MEMORY.md top-to-bottom for blocks matching the "Consolidate Routing Table" below. A "block" is:
- An H2 or H3 section with stable, factual, or reference-shaped content, OR
- A subsection or paragraph that is dated > 30 days and not in the active hot-items list, OR
- An inline entity exceeding 10 lines or 5 mentions (overlap with Compile's entity-promotion rule is fine — first-mover wins).

Skip:
- The Hot / Time-Sensitive section
- The Memory Structure Map (lives in MEMORY.md by definition)
- The "Active People (with files)" anchor table (already pointer-shaped)
- The current model-config summary line
- The Active Cron Jobs *summary* row count (terse table can stay; long Known-Issues subsection can move)
- Anything explicitly tagged `[hot]`, `[active]`, or dated within the last 14 days
- Foundation files — Consolidate does not touch them

**Phase 2 — Classify (Routing Table)**

| Pattern in MEMORY.md | Destination | Notes |
|---|---|---|
| Stable technical/internal facts (how X works) | `knowledge/topics/<name>.md` | Append under dated subhead if file exists |
| Stable how-tos/workflows | `knowledge/procedures/<name>.md` | Same |
| Deep-dive analyses or research notes | `knowledge/research/<name>.md` | Same |
| Behavioral rules / "do X not Y" | `memory/lessons/<domain>.md` | Use existing domain split if present |
| Time-stamped past incidents that are now lessons | `memory/lessons/<domain>.md` | |
| Inline person entity exceeding promotion threshold | `memory/contacts/<channel>-<id>.md` | If file exists, append; else create |
| Inline group entity exceeding promotion threshold | `memory/groups/<channel>-g-<name>.md` | |
| Pass-log footer entries (Curate pass / Compile pass) older than the last 5 | `archive/digests/<existing-or-new>.md` or fold into the matching weekly digest if one exists | |
| Cron known-issues that are dated and resolved (✅ Cleared) | `archive/cron-issues-log-YYYY-Qn.md` (create if needed) | |
| Stale temporary state (e.g., a "this week we're house-sitting" note from 90 days ago) | `archive/daily/YYYY-MM/` | Append to a dated note file |
| Ambiguous / doesn't fit | `memory/notes/` (staging) — Compile sorts later | Last-resort lane |
| Foundation-file drift signal | **NO MOVE** — record in manifest under "Recommendations to Calibrate" | |

**Phase 3 — Plan (Dry Run)**
Build an in-memory plan of all moves:
- source block (start line, end line, byte range)
- destination file + insertion strategy (create / append / append-under-dated-header)
- replacement pointer text (1 line, format below)
- estimated lines moved (running total against 200-line budget)

**Phase 4 — Apply**
For each planned move (in source order, until budget exhausted):
1. Verbatim copy source block into destination file. If destination exists and the block is being added, append under `## Imported from MEMORY.md — YYYY-MM-DD` (or merge into matching existing subhead if obvious).
2. Replace the source block in MEMORY.md with a single pointer line at the same indentation/list-context:
   ```
   - [<descriptive label>](<relative-path>) — <one-clause reason / what's there>
   ```
3. If the move created a new file, update the **Memory Structure Map** in MEMORY.md.
4. Use `trash`-equivalent semantics for any cleanup; never delete content.

**Phase 5 — Manifest**
Write `archive/consolidate-manifests/YYYY-MM-DD-HHMM.md`:

```markdown
# Consolidate Manifest — YYYY-MM-DD HH:MM PT

**Trigger:** scheduled | manual
**MEMORY.md before:** N lines
**MEMORY.md after:** M lines (Δ -K lines)
**Severity:** OK | TRIPWIRE | HIGH

## Moves
- `<MEMORY.md §section>` → `<dest path>` (L lines) — pointer: "<text>"
- ...

## New Files Created
- `<path>` — <one-line description>

## Recommendations to Calibrate
- Foundation drift: `<file>` shows <signal>; consider <suggestion>
- Junk-drawer risk: `memory/notes/` grew by N lines this run

## Skipped (out of budget)
- `<MEMORY.md §section>` — N lines, deferred to next run

## Notes
- <any anomalies, conflicts, or follow-ups>
```

**Phase 6 — Pass-Log Rotation (last)**
If MEMORY.md has more than 5 trailing `*Curate pass:* / *Compile pass:*` log lines in its footer, move the oldest into the matching weekly digest in `archive/digests/` (or, if no match, append to the manifest's "Notes" section). Leave the most recent 5.

### Pointer Format Rules

- One line. Use a markdown list item if inside a list, otherwise an italic note: `*See <path> for details.*`
- Always relative path from workspace root.
- Include a one-clause reason so the pointer is itself useful working memory.
- Do **not** rewrite or summarize the moved content — verbatim only at the destination.

### What Consolidate Will NOT Do

- Edit `AGENTS.md`, `SOUL.md`, `IDENTITY.md`, `USER.md`, `TOOLS.md` (recommendations only — see manifest section).
- Extract lessons, resolve contradictions, graduate memory→knowledge, or compress/summarize content.
- Delete anything. Use moves with pointers.
- Touch `memory/daily/` files (Compile's archival domain).
- Run if MEMORY.md is ≤ 350 lines (no-op manifest, exit).

### Safety / First-Run Protocol

- The **first applied** Consolidate run on a workspace must be **dry-run only**: produce a candidate manifest with no edits, written to `archive/consolidate-manifests/YYYY-MM-DD-HHMM-dryrun.md`. Human approves before the first real apply.
- Subsequent scheduled runs apply automatically, bounded by the 200-line budget and the no-op-at-350 rule.
- After each apply: re-read MEMORY.md once and verify the pointer count matches the moves count. Mismatch → flag in manifest, do not retry.

### Cadence Rationale

Twice-weekly (Wed + Sat) is the smallest cadence that can absorb mid-week Curate inflow without letting MEMORY.md drift past the tripwire before the weekly Compile pass. Sat is positioned before Sun Compile so manifests are fresh for Phase 0 ingestion.

---

## 2. EDITS to `pipeline/CURATE.md`

### A. Tighten the MEMORY.md routing entry

In the **Routing Decision Tree** table, replace the `MEMORY.md` row:

> | Hot/current, needs quick access? | `MEMORY.md` | Actively relevant now (keep under 10 lines; beyond that, route to specific file and leave a pointer) |

with:

> | Hot/current AND under active follow-up? | `MEMORY.md` | Last lane. See "MEMORY.md write rules" below. |

### B. Add a new section after the Routing Decision Tree

```markdown
## MEMORY.md Write Rules

`MEMORY.md` is working memory + index, not a warehouse. Consolidate runs
twice-weekly to evict stable content; Curate's job is to not put stable
content there in the first place.

Write to MEMORY.md only if **all** of the following hold:

- The item is currently being acted on, OR has an explicit time-window
  relevance (deadline, reminder, "this week").
- One concise entry, ≤ 5 lines.
- Removing it in 30 days would not lose information (because it has
  either expired or has been relocated to a canonical home with a
  pointer).

If any condition fails, route to the canonical destination from the
table above. Optionally leave a single pointer line in MEMORY.md only
if the pointer itself is useful working-memory context.

Never write to MEMORY.md:
- Long technical insights or "how X works" → `knowledge/topics/`
- Multi-step how-tos → `knowledge/procedures/`
- Behavioral rules → `memory/lessons/`
- Per-person updates → `memory/contacts/<file>`
- Per-group updates → `memory/groups/<file>`
- Cron tables, model catalogs, configuration reference → `knowledge/topics/`
  or `knowledge/procedures/`
```

### C. Add to "Operational Notes"

```markdown
- **Don't bloat working memory.** When a MEMORY.md entry trends past 5 lines,
  promote first, leave a pointer second. Consolidate will still catch
  oversights, but the pipeline is healthier when Curate routes correctly
  on day one.
```

### D. Update the header block

Add a one-line "see also":

```
**See also:** CONSOLIDATE.md (twice-weekly working-memory eviction)
```

---

## 3. EDITS to `pipeline/COMPILE.md`

### A. Add new Phase 0 (before existing Phase 1)

```markdown
## Phase 0: Ingest Consolidate Manifests

Compile runs after two Consolidate passes (Wed + Sat). Read the manifests
so synthesis sees what moved.

### 0.1 Read

List `archive/consolidate-manifests/*.md` from the last 7 days
(skip files ending in `-noop.md` for content; still note them for
pipeline-health metrics).

### 0.2 For Each Move

- Note the source (MEMORY.md §section) and destination.
- Read the destination file once to confirm the content arrived intact.
- Treat moved-but-uncategorized content (anything routed to
  `memory/notes/`) as a candidate for Phase 4 (lesson extraction) or
  Phase 5 (graduation).

### 0.3 Surface Recommendations

Collect every "Recommendations to Calibrate" entry from this week's
manifests. Pass them through to Phase 7 (Weekly Digest) under a new
`## Forwarded to Calibrate` section so Calibrate sees them in one place.

### 0.4 Health Signal

Track and include in the digest:
- Number of Consolidate runs this week (expect 2)
- Total lines moved
- Number of no-op runs (MEMORY.md was healthy)
- Severity flags (TRIPWIRE / HIGH)

### 0.5 Skip if no manifests

If no manifests exist for the week, note it (Consolidate may be off,
misconfigured, or new) and proceed. Compile must remain robust to
Consolidate being absent.
```

### B. Slim Phase 1 (File Organization)

- Keep 1.1 (archive old daily files) and 1.3 (cleanup empty/duplicate). These remain Compile's job.
- Keep 1.2 (sweep loose session summaries) — still Compile's domain since Consolidate doesn't touch `memory/daily/` or root session-summary files.

### C. Reframe Phase 2 (Entity Promotion)

Change the opening framing to explicitly note the Consolidate overlap:

```markdown
## Phase 2: Entity Promotion (Backstop)

Consolidate handles routine MEMORY.md entity promotion twice weekly.
Phase 2 here is a **backstop** for entities Consolidate did not catch
— typically those embedded in non-MEMORY.md files (e.g., a contact
file growing too large, or a topic file ballooning past usefulness)
or where weekly synthesis suggests a different route than the surface
heuristic Consolidate used.
```

Keep the threshold table and steps as-is, but apply them to `memory/`,
`knowledge/`, and `MEMORY.md` (in that priority).

### D. Trim Phase 3 (Staleness Pruning)

Remove any language implying Compile is the primary owner of MEMORY.md size control. Keep staleness pruning for `memory/`, `knowledge/`, and archive contents. Replace any MEMORY.md-specific paragraph with:

```markdown
### 3.X MEMORY.md

MEMORY.md size is owned by Consolidate. If Phase 0 health signals show
MEMORY.md still exceeded the 400-line tripwire across the week despite
two Consolidate runs, flag it under "Forwarded to Calibrate" rather
than pruning here.
```

### E. Phases 4–6 (Lessons, Graduation, Patterns) — unchanged

These remain Compile's core synthesis value. No edits required, except: in Phase 5 (Graduation Assessment), under "Scan Locations", add:

```
- Recently Consolidated targets (especially knowledge/topics/ and
  memory/notes/ destinations from this week's manifests) — content
  that was moved but not yet graduated may now be ready.
```

### F. Phase 7 (Weekly Digest) — add subsections

```markdown
## Consolidate Activity
- Runs this week: [count] (expect 2)
- Total lines moved: [count]
- No-op runs: [count]
- MEMORY.md before/after week: [start] → [end]
- Severity flags: [list]

## Forwarded to Calibrate
- [recommendation from manifest]
- ...
```

### G. Update timing table

Add Phase 0 row (~1–2 min). Total moves to ~17–27 min.

### H. Header block update

Add:

```
**Reads:** archive/consolidate-manifests/*.md from the last 7 days (Phase 0).
```

---

## 4. EDITS to `pipeline/CALIBRATE.md`

### A. Refocus header purpose

Adjust the Purpose paragraph to lead with learning quality:

```markdown
Calibrate's center of gravity is **learning quality** — validating lessons,
filling procedure gaps, updating skill notes — with a lighter pass on
foundation drift and pipeline health (including a dedicated review of
Consolidate behavior).
```

### B. Add Phase 2.X — Consolidate Review

Inside Phase 2 (Pipeline Health), add a new subsection between 2.3 (Compile Review) and 2.4 (Structure Map Check):

```markdown
### 2.X Consolidate Review

Sample the last 4–8 Consolidate manifests (`archive/consolidate-manifests/`).

- **Working-memory health:** Did `MEMORY.md` stay ≤ 400 lines for ≥ 80%
  of the month? If not, consider raising cadence (e.g., adding Mon)
  or relaxing the per-run 200-line budget.
- **Pointer integrity:** Spot-check 5 random pointers from manifests —
  does the destination still exist? Is the content intact?
- **Junk-drawer risk:** Has any single destination (especially
  `memory/notes/`) grown disproportionately? Flag for Compile's
  Phase 5 graduation focus.
- **Aggressiveness:** Sample 5 moves — were any premature (content that
  should have stayed hot)? If so, propose tightening the routing table.
- **No-op rate:** What fraction of runs were no-ops? High no-op rate
  is fine (it means the system is healthy). Persistent zero no-ops
  with a still-bloating MEMORY.md means Curate is over-writing to
  MEMORY.md — fix at the Curate layer.
```

### C. New Phase 3 — Process Forwarded Recommendations

Insert before the existing Lessons Validation phase (which becomes Phase 4):

```markdown
## Phase 3: Recommendations from Consolidate

Consolidate cannot edit foundation files. It logs recommendations under
"Recommendations to Calibrate" in each manifest, and Compile forwards
them through the weekly digest.

### 3.1 Collect

Read `archive/digests/*.md` since the last Calibrate, plus any manifests
Compile may have missed. Aggregate all forwarded recommendations.

### 3.2 Decide

For each recommendation:
- Is it a clear foundation-file edit (AGENTS / SOUL / IDENTITY / USER / TOOLS)?
- Is the evidence sufficient (multiple signals, not a single anecdote)?
- Does the change cross a human-judgment line (identity, security, trust)?

### 3.3 Act

- **Clear, low-stakes edits:** Apply directly, log in audit report.
- **Identity / security / trust changes:** Propose in audit report under
  "Human Review Needed" — do not apply.
- **Stale or contradicted recommendations:** Drop, note why.
```

### D. Renumber subsequent phases

Old Phase 3 (Lessons Validation) → Phase 4. Old Phase 4 (Meta) → Phase 5.

### E. Output template additions

In the audit-report template, add:

```markdown
## Consolidate Health
- Runs/month: [count] (expect ~8)
- No-op rate: [pct]
- MEMORY.md monthly avg: [lines]
- Pointer integrity sample: [pass/fail]
- Findings: [list]

## Recommendations Processed
- Applied: [count]
- Forwarded to human: [count]
- Dropped: [count]
```

### F. Trim foundation review

Phase 1 (Foundation File Review) becomes a **light** drift check rather than the centerpiece — explicit one-line note: "Light pass. Heavy edits go through Phase 3 (forwarded recommendations) so the trail is auditable."

### G. Timing table update

Reduce Foundation Review to 3–4 min, add Phase 3 (3–4 min), keep total in the 17–22 min range.

---

## 5. EDITS to `pipeline/HOW-IT-WORKS.md`

### A. Replace the "System Architecture: The 4C Pipeline" diagram

Replace the existing 4C ASCII pipeline diagram with a 5C version. Inject the Consolidate box between Curate and Compile:

```
                              ┌─────────────────┐
                              │  CONVERSATIONS  │
                              └────────┬────────┘
                                       ▼
┌──────────────────────────────────────────────────────────────────────────┐
│                         COLLECT (bi-hourly, Haiku)                       │
│  Scans recent sessions, extracts notable content into daily files        │
└──────────────────────────────────────┬───────────────────────────────────┘
                                       ▼
                              ┌─────────────────┐
                              │  memory/daily/  │
                              └────────┬────────┘
                                       ▼
┌──────────────────────────────────────────────────────────────────────────┐
│                          CURATE (daily, Sonnet)                          │
│      Routes valuable content (concise to MEMORY.md, full to canonical)   │
└──────────────────────────────────────┬───────────────────────────────────┘
                                       ▼
┌──────────────────────────────────────────────────────────────────────────┐
│                  CONSOLIDATE (Wed + Sat, Sonnet/gpt-5.5)                 │
│  Verbatim relocation of stable MEMORY.md content + pointers + manifest   │
│  Mover/indexer only — no synthesis, no foundation edits, no deletion     │
└──────────────────────────────────────┬───────────────────────────────────┘
                                       ▼
                ┌──────────────────────────────────────┐
                │ archive/consolidate-manifests/*.md   │
                │ (read by Compile Phase 0)            │
                └──────────────┬───────────────────────┘
                               ▼
┌──────────────────────────────────────────────────────────────────────────┐
│                          COMPILE (Sunday, Opus)                          │
│  Phase 0: ingest manifests · lessons · graduation · patterns · digest    │
└──────────────────────────────────────┬───────────────────────────────────┘
                                       ▼
┌──────────────────────────────────────────────────────────────────────────┐
│                         CALIBRATE (Monthly, Opus)                        │
│  Lessons · procedures · skill notes · Consolidate review · forwarded recs│
└──────────────────────────────────────────────────────────────────────────┘
```

### B. Update "Stage 2.5: Consolidate" — new section

Insert between Stage 2 (Curate) and Stage 3 (Compile) as a new "Stage 2.5":

```markdown
### Stage 2.5: Consolidate (Twice-Weekly)

**Model:** Sonnet (gpt-5.5 lane initially)
**Cadence:** Wed + Sat ~05:00 PT
**Purpose:** Keep MEMORY.md healthy as working memory + index.

Consolidate runs twice a week to evict stable content from MEMORY.md to
its canonical home. It is a **mover/indexer**, not a synthesizer:
verbatim relocation, one-line pointers, and a manifest. Targets ≤350
lines, with a 400-line tripwire and a 200-lines-per-run move budget.

Compile reads the past week's manifests as Phase 0 input, so moved
detail is preserved for weekly synthesis. Calibrate reviews
Consolidate's behavior monthly and processes forwarded recommendations
for foundation files.

**Output:** `archive/consolidate-manifests/YYYY-MM-DD-HHMM.md`

See `memory/.system/CONSOLIDATE.md` for full instructions.
```

### C. Update Timing Summary table

Add a Consolidate row, fix existing rows to reflect the slimmer Compile / refocused Calibrate:

| Job | Frequency | Model | Purpose |
|-----|-----------|-------|---------|
| Collect | Bi-hourly | Haiku | Raw capture → daily files |
| Curate | Daily | Sonnet | Route → long-term storage (concise to MEMORY.md) |
| **Consolidate** | **2× weekly (Wed + Sat)** | **Sonnet (gpt-5.5)** | **Evict stable MEMORY.md content to canonical homes** |
| Compile | Weekly (Sunday) | Opus | Manifest ingest, lessons, graduation, patterns, digest |
| Calibrate | Monthly (1st) | Opus | Lessons, procedures, skill notes, Consolidate review |
| Tidy | Weekly or as-needed | Haiku | Misplaced file detection |

### D. Update "Complete Job System" ASCII

Add a SEMI-WEEKLY block between CONTINUOUS and WEEKLY:

```
SEMI-WEEKLY (keeps working memory healthy):
─────────────────────────────────────────────────────────────────
  CONSOLIDATE (Wed + Sat)
  Sonnet
       │
  Read MEMORY.md
  Identify stable content
  Move verbatim → canonical homes
  Leave one-line pointers
  Write manifest
       │
  archive/consolidate-manifests/
```

### E. Update Isolated-Sessions table

Add Consolidate row: `isolated · Sonnet · Wed + Sat`.

### F. Update Related Documents table

Add row for `CONSOLIDATE.md`.

### G. Update "After a conversation" bullet list

Insert a bullet after Curate-flavored point:

> 5. Stable content drifts out of working memory via **Consolidate** (twice weekly), preserved verbatim at its canonical home with a pointer left behind.

---

## 6. EDITS to `README.md`

### A. Update "The 4C Pipeline" → "The 5C Pipeline"

Rename header. Replace the ASCII pipeline diagram (currently lines 60–88 in README.md) with a 5C version that includes a SEMI-WEEKLY block for Consolidate, mirroring HOW-IT-WORKS:

```
CONTINUOUS ──────────────────────────────────────────────────
  🪣 COLLECT (bi-hourly · Haiku)        📚 CURATE (daily · Sonnet)
     Scan sessions                         Route to long-term homes
     Append to daily logs                  Concise to MEMORY.md
          │                                    │
          ▼                                    ▼
     memory/daily/                         memory/* + knowledge/* + MEMORY.md

SEMI-WEEKLY ─────────────────────────────────────────────────
  🧹 CONSOLIDATE (Wed + Sat · Sonnet)
     Evict stable MEMORY.md content
     Verbatim relocate → pointer → manifest
     archive/consolidate-manifests/

WEEKLY ──────────────────────────────────────────────────────
  🔨 COMPILE (Sunday · Opus)
     Phase 0: ingest manifests │ Extract lessons
     Archive old daily files   │ Graduate content
     Find patterns             │ Weekly digest

MONTHLY ─────────────────────────────────────────────────────
  🔬 CALIBRATE (1st · Opus)
     Lessons & procedures focus
     Consolidate health review
     Process forwarded recommendations
     Light foundation drift check
```

### B. Add a Consolidate section between "Curate" and "Compile"

```markdown
### Consolidate — The Mover 🧹

**2× weekly (Wed + Sat) · Sonnet · Isolated session**

Keeps MEMORY.md from becoming a warehouse. Twice a week, scans MEMORY.md
for stable content (long technical notes, cooled topics, completed
incidents) and relocates each block verbatim to its canonical home in
`knowledge/`, `memory/`, or `archive/`, leaving a one-line pointer
behind and writing a manifest of every move.

Mover/indexer only — no summarizing, no lesson extraction, no
foundation-file edits. Targets ≤350 lines with a 400-line tripwire
and a 200-line per-run move budget.
```

### C. Update existing "Compile" and "Calibrate" sections

- Compile: "A single smart pass…" → "A single smart weekly pass that ingests recent Consolidate manifests, archives old daily files, extracts lessons, graduates content, finds patterns, and produces a weekly digest. Routine MEMORY.md hygiene is owned by Consolidate, freeing Compile for synthesis work."
- Calibrate: "Steps back to ask: is the system working? Reviews lessons, fills procedure gaps, updates skill notes, audits Consolidate health, processes recommendations Consolidate forwarded for foundation files, and runs a light drift check on identity files."

### D. Update the "Pipeline Documentation" table

Add a CONSOLIDATE.md row between CURATE.md and COMPILE.md.

### E. Update "Why 'Crustacean'?" footer

Update the 4C list:
> Also, the 5C's: **C**ollect, **C**urate, **C**onsolidate, **C**ompile, **C**alibrate. C is for Crustacean. 🦀

### F. Update "Getting Started"

Step 3 currently says "Set up cron jobs for each of the 4C stages". Change to "5C stages" and add a note to read the cron table in HOW-IT-WORKS.md including Consolidate's Wed+Sat cadence.

---

## 7. Cron Model & Cadence (downstream — `/root/.openclaw/cron/jobs.json`)

This change is downstream of the doc edits. Spec for the cron entry:

```jsonc
{
  "name": "consolidate",
  "schedule": "0 5 * * 3,6",   // Wed + Sat 05:00 (server tz; verify PT alignment)
  "model": "openai-codex/gpt-5.5",   // matches current 4C model lane
  "session": "isolated",
  "wakeMode": "next-heartbeat",
  "prompt": "Run the Consolidate job per memory/.system/CONSOLIDATE.md. Begin with Phase 0 preflight; if MEMORY.md ≤ 350 lines, write a no-op manifest and exit. Otherwise proceed through phases 1–6 with the 200-line per-run budget. First run on this workspace must be dry-run only."
}
```

Cadence/model rationale:
- **Wed + Sat** — Wed absorbs mid-week Curate inflow; Sat positions a fresh manifest for Sun Compile Phase 0. Avoid same-day overlap with Curate (06:00 PT) by running 05:00.
- **gpt-5.5** initially — matches the existing 4C job lane (Collect/Curate/Compile/Calibrate are all `gpt-5.5` in current jobs.json) for operational consistency. Upgrade to Sonnet via Claude CLI subscription path is a separate later workstream (already noted in `PROJECT_PROGRESS.md`).
- **Isolated session** — same rationale as the rest of the pipeline; Consolidate writes files and exits.
- **`wakeMode: next-heartbeat`** — matches the local cron best-practice doc.

While editing `jobs.json`, fix the existing **Curate** entry: `wakeMode: now` → `wakeMode: next-heartbeat` (separate, pre-existing follow-up noted in `PROJECT_PROGRESS.md` activity log).

Schedule summary post-Consolidate:

| Job | Schedule | Model | wakeMode | Session |
|---|---|---|---|---|
| Collect | bi-hourly (every 2h) | gpt-5.5 | next-heartbeat | isolated |
| Curate | daily 06:00 PT | gpt-5.5 | next-heartbeat **(fix)** | isolated |
| **Consolidate** | **Wed + Sat 05:00 PT** | **gpt-5.5** | **next-heartbeat** | **isolated** |
| Compile | Sun 07:00 PT | gpt-5.5 | next-heartbeat | isolated |
| Calibrate | 1st of month 08:00 PT | gpt-5.5 | next-heartbeat | isolated |

---

## 8. Initial MEMORY.md Content Classes to Relocate (Dry-Run Targets)

Based on a read of the live `/root/.openclaw/workspace/MEMORY.md` (~520 lines on 2026-05-03), the following blocks are concrete first-pass candidates for the dry-run manifest. Total estimated reduction: ~520 → ~210–250 lines. Well under the 350-line target without exhausting the 200-line per-run budget (will likely need 2 passes).

| # | Block (current MEMORY.md location) | Approx lines | Destination | Pointer text suggestion |
|---|---|---|---|---|
| 1 | "🔬 Technical Insights" → OpenClaw internals (debounce, queue interrupt, sessions, Telegram permissions, edit detection, .skill format, memory-search architecture, bootstrap truncation) | ~50 | `knowledge/topics/openclaw.md` (append under `## Internals — Imported from MEMORY.md 2026-05-03`) | `*OpenClaw internals (debounce, queue, sessions, Telegram, .skill, memory search) → knowledge/topics/openclaw.md*` |
| 2 | "🔬 Technical Insights" → NVIDIA Kimi K2.5 integration | ~6 | `knowledge/topics/nvidia-nim.md` (new) | `*NVIDIA Kimi K2.5 integration details → knowledge/topics/nvidia-nim.md*` |
| 3 | "🔬 Technical Insights" → Venice Model two-layer config | ~7 | `knowledge/procedures/venice-model-registration.md` (new) | `*Venice two-layer model config → knowledge/procedures/venice-model-registration.md*` |
| 4 | "🔬 Technical Insights" → Zero-Token /diem plugin pattern | ~7 | `knowledge/procedures/zero-token-plugin-pattern.md` (new) | `*Zero-token plugin pattern (/diem) → knowledge/procedures/zero-token-plugin-pattern.md*` |
| 5 | "🔬 Technical Insights" → Brave Search rate-limit note | ~6 | `knowledge/topics/brave-search.md` (new) | `*Brave Search free-tier rate-limit notes → knowledge/topics/brave-search.md*` |
| 6 | "🔬 Technical Insights" → Claude API plan limitations | ~5 | `knowledge/topics/claude-api.md` (new) | `*Claude API plan/scope limits → knowledge/topics/claude-api.md*` |
| 7 | "🦞 Identity & Setup" → historical model alternatives + Venice list + dated test results | ~12 | `knowledge/topics/sean-model-config.md` (new) | Replace block; keep current default + 4C lane as a 3-line summary in MEMORY.md |
| 8 | "🎨 Image Generation" → cost discipline (3 lines + rule) | ~5 | `memory/lessons/image-generation.md` (new or merge) | `*Image generation cost discipline → memory/lessons/image-generation.md*` |
| 9 | "🎨 Image Generation" → storage strategy + sub-agent info | ~10 | `knowledge/procedures/image-generation.md` (new) | `*Image generation storage + sub-agent pattern → knowledge/procedures/image-generation.md*` |
| 10 | "🔍 Research Interests" → Privacy/DeFi landscape | ~10 | `knowledge/topics/privacy-defi-2026-q1.md` (new) | `*Privacy/DeFi landscape (2026-Q1 snapshot) → knowledge/topics/privacy-defi-2026-q1.md*` |
| 11 | "🔍 Research Interests" → ETH staking signal (2026-02-13) | ~3 | `archive/daily/2026-02/eth-staking-signal.md` | (drop pointer; old single signal) |
| 12 | "🔍 Research Interests" → Specialized Agents Framework + Anna Goatier | ~10 | `knowledge/research/specialized-agents-framework.md` (new) | `*Specialized agents framework / Anna Goatier (with Dan) → knowledge/research/specialized-agents-framework.md*` |
| 13 | "📚 OpenClaw Knowledge Practice" → "Firm rule" + 2026-02-11 incident | ~8 | `memory/lessons/openclaw-operations.md` (existing) | `*OpenClaw docs-first practice + 2026-02-11 incident → memory/lessons/openclaw-operations.md*` |
| 14 | "📚 OpenClaw Knowledge Practice" → Memory architecture decision (2026-02-25) | ~5 | `knowledge/research/people-index-decision.md` (new) — **flag in manifest as candidate to seed `memory/decisions/` (deferred-expansion folder per MEMORY.md)** | `*Decision: contacts/ as source of truth, no global people.md → knowledge/research/people-index-decision.md*` |
| 15 | "🔐 Security Rules" → Incidents subsection (3 dated entries) | ~6 | `memory/lessons/security-incidents.md` (new) | `*Past security incidents (2026-02-10, 02-14, 02-27) → memory/lessons/security-incidents.md*` |
| 16 | "💬 Communication Guidelines" → Heartbeat system bullet list | ~14 | `knowledge/procedures/heartbeat-system.md` (new) | `*Heartbeat capture taxonomy → knowledge/procedures/heartbeat-system.md*` |
| 17 | "💬 Communication Guidelines" → Telegram formatting + reactions | ~10 | `knowledge/procedures/telegram-formatting.md` (new) | `*Telegram formatting + reaction set → knowledge/procedures/telegram-formatting.md*` |
| 18 | "💬 Communication Guidelines" → Relaying messages CRITICAL block | ~8 | `memory/lessons/communication.md` (new or merge) | `*Relay-to-original-requester rule (2026-02-14 cupcake incident) → memory/lessons/communication.md*` |
| 19 | "🏠 Household Context" → Burton Family House (week of 2026-02-14) | ~6 | `archive/daily/2026-02/burton-house-sit.md` | (drop pointer; stale) |
| 20 | "📋 Active Cron Jobs" → Known Issues subsection (✅ Cleared rows) | ~10 | `archive/cron-issues-log-2026-Q1.md` (new) | `*Resolved cron known-issues 2026-Q1 → archive/cron-issues-log-2026-Q1.md*` |
| 21 | Inline entities: Burton Family Dogs (2026-02-14 stale) | ~3 | `archive/daily/2026-02/burton-house-sit.md` | (drop) |
| 22 | Inline entities: Claw & Kin / Los Crabos / Lobster Ledger (already promoted to group files) | ~12 | trim each to a 1-line pointer to the existing `memory/groups/<file>.md` | per-line replacement |
| 23 | Identity notes (Madison/Roxi block — already in their respective contact files) | ~14 | trim to 1-line pointers per person | replace with two pointer lines |
| 24 | Pass-log footer (`*Curate pass:*` / `*Compile pass:*` lines) older than the most recent 5 | ~16 | merge each into matching `archive/digests/YYYY-MM-DD-weekly.md` (or append to manifest "Notes" if no match) | (replace footer block with last 5 lines) |

What stays in MEMORY.md (DO NOT MOVE):
- Memory Structure Map
- Identity & Setup (current default model only — the "Default/Main" line)
- Active Cron Jobs (terse table only)
- Trust Tiers
- Active People (with files) anchor table
- Hot / Time-Sensitive section in full
- Inline anchor pointers to all promoted entities
- Last 5 pass-log footer lines

Recommendations for Calibrate (logged in manifest, not applied by Consolidate):
- "🦞 Identity & Setup" historical content suggests `IDENTITY.md` may have drifted — review at next Calibrate.
- "🔐 Security Rules" sits in MEMORY.md but its rules are foundational — consider whether the firm-rule list should live in `AGENTS.md` (Calibrate decision).
- The "Deferred Expansions" table mentions `memory/decisions/`; recommendation #14 is the first ADR-class item — Calibrate may decide to materialize the folder.

---

## 9. Verification (How to Test End-to-End)

### Doc-only verification (this PR)

- `pipeline/CONSOLIDATE.md` exists, defines mover/indexer, manifest, preservation invariant, no-op-at-350.
- `pipeline/CURATE.md` has "MEMORY.md Write Rules" section and the routing-table row is updated.
- `pipeline/COMPILE.md` has Phase 0 (Ingest Consolidate Manifests) and Phase 7 has "Consolidate Activity" + "Forwarded to Calibrate" subsections.
- `pipeline/CALIBRATE.md` has Phase 2.X (Consolidate Review) and Phase 3 (Recommendations from Consolidate); foundation review trimmed.
- `pipeline/HOW-IT-WORKS.md` and `README.md` show 5C ordering, semi-weekly Consolidate stage, and updated tables.
- All cross-references between docs are bidirectional (CONSOLIDATE ↔ CURATE/COMPILE/CALIBRATE).

### Live verification (downstream of doc merge)

- Workspace sync: copy `pipeline/*.md` to `/root/.openclaw/workspace/memory/.system/`.
- Add the Consolidate cron entry to `/root/.openclaw/cron/jobs.json`. Fix Curate `wakeMode`.
- **First applied run is dry-run only**: trigger Consolidate manually, confirm `archive/consolidate-manifests/YYYY-MM-DD-HHMM-dryrun.md` is produced with no edits. Human reviews the candidate moves against the table in §8.
- Approve dry-run → run real Consolidate. Verify:
  - MEMORY.md drops below 350 lines (or the 200-line budget was exhausted with `Skipped (out of budget)` populated).
  - Every move shows up at the destination verbatim.
  - Every moved block has exactly one pointer in MEMORY.md.
  - New files added under `knowledge/topics/`, `knowledge/procedures/`, etc. are in the Memory Structure Map.
- Wait for next Sunday Compile run. Verify:
  - Compile Phase 0 reads the manifests.
  - Weekly digest contains "Consolidate Activity" and "Forwarded to Calibrate" sections.
- Wait for first Calibrate after Consolidate is live. Verify:
  - Audit report contains "Consolidate Health" and "Recommendations Processed" sections.
  - Forwarded recommendations are either applied with audit log, or escalated under "Human Review Needed".

### Health metrics to track

- MEMORY.md line count over time (target: ≤350 sustained).
- Consolidate no-op rate (healthy mid-range; persistent zero with bloat → Curate is the leak).
- Manifest pointer-integrity sample (Calibrate Phase 2.X).
- `memory/notes/` size (junk-drawer guard).

---

## 10. Out of Scope for This Round

- Consolidate v2: extending to `memory/contacts/` and `memory/groups/` profile drift.
- Cron-triggered Claude Code / CLI subscription pattern (separate workstream; placeholder note in `PROJECT_PROGRESS.md`).
- `memory/decisions/` folder materialization (Calibrate decision after first ADR-class manifest entry).
- Reminders skill (work Clawdia) updates for the CLI subscription pattern.

---

*Plan written 2026-05-03. Architecture review C: implementation-spec lens. Read-only — no files were edited in producing this plan.*
