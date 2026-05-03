# CURATE.md

Instructions for the **daily curation job** — routing valuable content from daily files into durable memory.

**Trigger:** Daily cron

**Model:** Sonnet

**Runtime:** Isolated session, ~10-15 min

---

## Purpose

Curate is the **librarian** of the memory system. Collect writes raw daily notes; Curate decides what deserves durable storage and routes it to the right canonical home.

Curate is a promotion step, but `MEMORY.md` is only one possible destination. Most full-detail content should land in contacts, groups, lessons, goals, reminders, knowledge, or notes/staging. `MEMORY.md` should receive only hot context, compact index entries, or pointers.

> **Curate routes; Compile synthesizes.**
>
> Curate promotes raw experience into durable memory. It does not stuff working memory, and it does not graduate episodic material to `knowledge/` — that judgment belongs to Compile.

---

## Lane Boundaries

### Curate Owns

- reading recent daily logs
- creating new canonical contact/group/topic files the first time durable material about a new entity appears
- deciding whether raw captured material deserves durable storage
- choosing the first canonical home
- appending/updating target memory and knowledge files
- adding small hot/index entries to `MEMORY.md` when needed
- deduplicating against the target file before writing

### Curate Does Not Own

- routine `MEMORY.md` size hygiene (Consolidate)
- weekly lesson sweeps or cross-week pattern synthesis (Compile)
- memory → knowledge graduation after maturity (Compile)
- foundation-file review or pipeline redesign (Calibrate)
- large rewrites of existing files
- graduating episodic material to `knowledge/` (maturity judgment belongs to Compile; route factual-looking items to `memory/notes/` when unsure)

---

## What to Do

1. Read the last 2-3 days of daily files (`memory/daily/`).
2. For each notable item, run the **Promotion Heuristic**.
3. If it passes, run the **Routing Decision Tree**.
4. Read the target file and check for duplicates.
5. Append or update — never overwrite whole files.
6. If writing to `MEMORY.md`, apply the strict working-memory write rules below.

---

## Promotion Heuristic

Curate when **any** of these apply:

- involves money, a date/deadline, or a named person
- states a preference, opinion, or boundary
- describes an event or milestone
- has been mentioned in 2+ separate sessions
- would meaningfully improve a future conversation if recalled

Skip:

- routine task completions
- transient troubleshooting with no reusable lesson
- vague statements
- anything already captured in the target file
- raw logs whose value is not yet clear but can remain in daily files until Compile

When in doubt, capture to the safest staging home (`memory/notes/`) rather than bloating `MEMORY.md`.

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
| Hot/current, needed at startup? | `MEMORY.md` | Active blockers, current priorities, index pointers |
| Factual reference, deep dive, or how-to — **already stable on day one**? | `knowledge/topics/`, `knowledge/research/`, or `knowledge/procedures/` | Only for material that is clearly transferable and stable at capture time; if maturity is uncertain, use `memory/notes/` and let Compile graduate it |
| Doesn't fit anywhere above? | `memory/notes/` | Staging ground — future Curate or Compile sorts it out |

---

## `MEMORY.md` Write Rules

`MEMORY.md` is working memory plus an index. It is not the archive.

**Default assumption: this item does NOT go in `MEMORY.md`.** Justify inclusion against the criteria below.

Write to `MEMORY.md` only when the item is:

- needed in the next few sessions without search, or
- an index/pointer that helps find canonical detail, or
- a high-priority current status/blocker, or
- a structure-map update for a newly created durable file.

Limits:

- default entry: **1-3 lines**
- hard cap per curated item: **5 lines** — no exceptions
- if it needs more than 5 lines, full detail goes in the canonical file with a short pointer (≤3 lines) in `MEMORY.md`

Good `MEMORY.md` entry:

```markdown
- **Project X:** Current blocker is Y; full context in `projects/x/PROJECT_PROGRESS.md`.
```

Bad `MEMORY.md` entry:

```markdown
- Multi-paragraph reconstruction of all decisions, logs, and implementation details.
```

---

## Conflict Resolution

- **Fresher wins** — if sources disagree, the more recent entry is correct.
- **Specific wins** — contact/group/project files override `MEMORY.md` for detailed context.
- **Primary home** — put full content in one place; leave pointers elsewhere.
- **Uncertain home** — use `memory/notes/`, not `MEMORY.md`.

---

## Deduplication

Before writing any file:

1. Read the target file first.
2. Scan for duplicates or near-duplicates.
3. Update existing entries rather than appending redundant ones.
4. If new info contradicts old info, update the old entry with provenance.

---

## Operational Notes

- Be concise: one clear sentence beats three vague ones.
- Date meaningful additions.
- Preserve voice when voice is the memory value.
- Do not store credentials or secrets.
- Avoid sensitive over-detail; route only what future behavior requires.
- Prefer canonical homes over `MEMORY.md`.

---

*Created: 2026-03-16; updated for 5C lane separation: 2026-05-03*
