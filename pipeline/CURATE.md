# CURATE.md

Instructions for the **daily curation job** — routing valuable content from daily files into long-term storage.

**Trigger:** Daily cron  
**Model:** Sonnet  
**Runtime:** Isolated session, ~10-15 min

---

## Purpose

Curate is the **librarian** of the memory system. While Collect dumps raw notes into daily files, Curate decides what's worth keeping long-term and routes it to the right home. This requires judgment — deciding relevance, picking the right destination, and avoiding duplication.

---

## What to Do

1. Read the last 2–3 days of daily files (`memory/daily/`)
2. For each notable item, run through the **Promotion Heuristic**
3. If it passes, run through the **Routing Decision Tree**
4. Check the target file for duplicates before writing
5. Append or update — never overwrite

---

## Promotion Heuristic

Curate when **any** of these apply:

- Involves money, a date/deadline, or a named person
- States a preference, opinion, or boundary
- Describes an event or milestone
- Has been mentioned in 2+ separate sessions
- Would meaningfully improve a future conversation if recalled

**Skip:** routine task completions, transient troubleshooting, vague statements, anything already captured in the target file.

**When in doubt:** capture it. Easier to prune later than recover lost context.

---

## Routing Decision Tree

Work through this table top-to-bottom. Route to the **first** match.

| Ask | Destination | Examples |
|---|---|---|
| About a specific person? | `memory/contacts/<channel>-<id>.md` | Preferences, life updates, relationship context |
| About a specific group/channel? | `memory/groups/<channel>-g-<name>.md` | Group dynamics, recurring topics, notable events |
| A lesson or prescriptive rule? | `memory/lessons/` | "Do X, not Y" — mistakes, gotchas, patterns |
| A goal or intention? | `memory/goals/` | Plans in progress, stated intentions |
| An idea or brainstorm? | `memory/ideas/` | Unvetted concepts, things to explore later |
| A reminder or time-sensitive? | `memory/reminders/` | Deadlines, upcoming events, follow-ups |
| An enumerative list? | `memory/lists/` | Watchlists, packing lists, trackers |
| Factual reference about a subject? | `knowledge/topics/` | How things work, what things are — stable, transferable |
| A deep dive or analysis? | `knowledge/research/` | Investigations, comparisons, findings |
| A how-to, workflow, or process? | `knowledge/procedures/` | Step-by-step instructions, config guides |
| Hot/current, needs quick access? | `MEMORY.md` | Actively relevant now (keep under 10 lines; beyond that, route to specific file and leave a pointer) |
| Doesn't fit anywhere above? | `memory/notes/` | Staging ground — future Curate or Compile sorts it out |

---

## Conflict Resolution

- **Fresher wins** — if sources disagree, the more recent entry is correct
- **Specific wins** — contact file overrides MEMORY.md for that person
- **Primary home** — put the full content in one place, leave pointers elsewhere
- **When uncertain** — capture it somewhere (easier to move than recover)

---

## Deduplication

Before writing any file:

1. Read the target file first
2. Scan for duplicates or near-duplicates
3. Update existing entries rather than appending redundant ones
4. If new info contradicts old info, update the old entry (fresher wins)

---

## Operational Notes

- **Be concise.** One clear sentence beats three vague ones.
- **Date your additions.** Prefix entries so we can track when things were learned.
- **Preserve voice.** Jokes, quotes, personality — keep the original flavor.
- **No sensitive info.** No medical details, financial specifics, or credentials.
- **Don't over-organize.** If something doesn't fit neatly, put it in the closest section.

---

*Created: 2026-03-16*
