# COLLECT.md

Instructions for the **bi-hourly collection job** — scanning recent sessions and extracting notable content into daily memory files.

**Trigger:** Bi-hourly cron  
**Model:** Haiku  
**Runtime:** Isolated session, ~3-5 min

---

## Purpose

Collect is the **recorder** of the memory system. It scans recent session transcripts and appends anything worth remembering to today's daily file. Append only — never edit, reorganize, or route content. That's Curate's job.

---

## What to Do

1. List recent sessions from the last ~2 days
2. For each session, scan for notable content (see extraction list below)
3. Append to `memory/daily/YYYY-MM-DD.md` (today's date)
4. Skip anything already captured in today's or yesterday's daily file

---

## What to Extract

- **Decisions made** — choices, conclusions, preferences stated
- **Plans & intentions** — things the user or others want to do
- **People & relationships** — names, who's who, connections, updates
- **Project context** — status updates, blockers, progress
- **Preferences** — likes, dislikes, how they want things done
- **Facts learned** — useful info, corrections, clarifications
- **Reminders & todos** — explicit requests to remember or do something
- **Ideas** — brainstorms, "what if" thoughts, things to explore
- **Running jokes & personality** — things that add flavor to future conversations

---

## Format

Append entries with brief context and timestamps:

```markdown
### [HH:MM] Session summary or topic

- Key point or fact extracted
- Another notable item
- [reminder] Explicit reminder if found
- [idea] Idea if captured
```

---

## What NOT to Do

- Don't reorganize or edit existing entries in daily files
- Don't route content to contacts/, groups/, knowledge/, etc. (Curate does that)
- Don't duplicate content already in today's or yesterday's file
- Don't capture routine task completions or transient troubleshooting
- Don't spam — if a session has nothing notable, skip it

---

## Cadence Rationale

Bi-hourly is frequent enough to catch everything without burning tokens on near-empty scans. Raw session data is preserved by OpenClaw regardless, so nothing is lost between runs. The 2-day lookback window provides overlap to catch sessions that straddled a previous Collect run.

---

*Created: 2026-03-16*
