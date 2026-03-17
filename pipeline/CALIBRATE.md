# CALIBRATE.md

Instructions for the **monthly calibration job** — a deep review of identity, memory system health, and process effectiveness.

**Trigger:** Monthly cron (1st of each month)  
**Model:** Opus  
**Runtime:** Isolated session, ~15-20 min

---

## Purpose

Calibrate steps back from the daily grind to ask: is the system working? Am I who I should be? It reviews foundation files for drift, audits the 4C pipeline, validates lessons, and proposes improvements.

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
- Has my "look" or "vibe" evolved?
- Any new defining characteristics?

### 1.3 USER.md
- Is the info about the human(s) still current?
- Anything learned that should be added?
- Any outdated context to remove?

### 1.4 AGENTS.md
- Are the operational rules working?
- Any guidelines I keep violating? (need adjustment)
- Any new patterns that should become guidelines?
- Are the session loading rules still correct?

### 1.5 TOOLS.md
- Are the documented paths still correct?
- Any new tools or credentials to add?
- Any stale entries to remove?
- Any skill friction noted in recent memories that should be documented here or in `skills/<skill>/NOTES.md`?

**Output:** Propose edits to any file that needs updating. Flag significant changes for human review.

---

## Phase 2: Pipeline Health

Audit the 4C cognitive pipeline.

### 2.1 Collect Review
- Check last 10 daily files — are they substantive or noise?
- Is Collect capturing the right things?
- Is anything important being missed?
- Is Collect too verbose? Too sparse?

### 2.2 Curate Review
- Are items routing to correct locations?
- Check for duplicates across files
- Sample 5 recent routing decisions — were they correct?
- Are there types of content consistently missed?

### 2.3 Compile Review
- Is entity promotion working?
- Is archival happening correctly?
- Are lessons being extracted?
- Is graduation happening appropriately?
- Are weekly digests useful?

### 2.4 Structure Map Check
- Does MEMORY.md's structure map reflect reality?
- Any orphaned files that should be linked?
- Any folders that have grown unwieldy?

### 2.5 Cadence & Model Check
- Are the current cadences right for each job?
- Any jobs that could use a cheaper or smarter model?

**Output:** Note any pipeline issues. Propose fixes or flag for human review.

---

## Phase 3: Lessons Validation

### 3.1 Review Existing Lessons
For each entry in `memory/lessons/`:
- Is the lesson still valid?
- Have circumstances changed?
- Any lessons I keep forgetting? (need better placement)
- Any duplicate or conflicting lessons?

### 3.2 Lessons Freshness
- Are there lessons older than 6 months?
- Do they still apply?
- Mark outdated lessons for archival → `archive/lessons/`

### 3.3 Lessons Coverage
- Are there domains without lessons? (gap)
- Are there experiences without extracted lessons? (missed opportunity)

**Output:** Updated lessons files, archival of stale lessons.

---

## Phase 4: Meta

A few quick questions to keep this process honest:
- Is Calibrate itself taking too long or missing important areas?
- Any architectural changes worth proposing to the memory system?
- Anything that should be flagged for the human that doesn't fit above?

---

## Output

After completing all phases, write to `archive/audits/YYYY-MM-calibrate.md`:

```markdown
# Monthly Calibrate — [Month Year]

## Foundation Files
- [ ] SOUL.md — [No changes / Proposed X]
- [ ] IDENTITY.md — [No changes / Proposed X]
- [ ] USER.md — [Updated X]
- [ ] AGENTS.md — [No changes / Proposed X]
- [ ] TOOLS.md — [Updated X]

## Pipeline Health
- Collect: [working/issues]
- Curate: [working/issues]
- Compile: [working/issues]
- Structure map: [accurate/updated]

## Lessons
- Validated: [count]
- Updated: [count]
- Archived: [count]

## Meta
- Process changes proposed: [none/list]

## Human Review Needed
- [ ] [Item requiring human decision]
```

---

## Timing

| Phase | Expected Duration |
|---|---|
| Foundation Review | 5-8 min |
| Pipeline Health | 4-6 min |
| Lessons Validation | 3-5 min |
| Meta | 1-2 min |
| **Total** | **~15-20 min** |

---

*Created: 2026-03-16*
