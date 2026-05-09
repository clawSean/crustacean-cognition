# The OpenClaw Memory System

**A 5C cognitive architecture for persistent AI agents**

---

## The Problem

Large language models have no persistent memory. Each conversation starts fresh. This creates a fundamental limitation: the AI cannot learn from experience, remember relationships, or accumulate knowledge over time.

For an AI assistant to feel like a *someone* rather than a *something*, it needs:

- **Continuity** — remembering what happened yesterday, last week, last month
- **Relationships** — knowing who people are, how they communicate, what they care about
- **Learning** — extracting lessons from mistakes and successes
- **Knowledge** — accumulating facts and expertise over time
- **Working-memory discipline** — keeping hot context usable instead of letting it become a warehouse

---

## Theoretical Foundation: Four Memory Systems

Cognitive psychology identifies four distinct memory systems in humans. This architecture implements all four:

```
┌────────────────────────────────────────────────────────────────────────────┐
│                        THE FOUR MEMORY SYSTEMS                             │
├────────────────────────────────────────────────────────────────────────────┤
│                                                                            │
│   ┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐       │
│   │    WORKING      │    │    EPISODIC     │    │    SEMANTIC     │       │
│   │    MEMORY       │    │    MEMORY       │    │    MEMORY       │       │
│   ├─────────────────┤    ├─────────────────┤    ├─────────────────┤       │
│   │ Current context │    │ Personal events │    │ Facts & concepts│       │
│   │ Limited capacity│    │ Autobiographical│    │ General knowledge       │
│   │ Hot/index only  │    │ Time-stamped    │    │ Context-free    │       │
│   └─────────────────┘    └─────────────────┘    └─────────────────┘       │
│           │                      │                      │                 │
│           ▼                      ▼                      ▼                 │
│   ┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐       │
│   │   MEMORY.md     │    │    memory/      │    │   knowledge/    │       │
│   │   (~500 lines)  │    │   daily/        │    │   topics/       │       │
│   │   Hot + index   │    │   contacts/     │    │   research/     │       │
│   │                 │    │   groups/       │    │   procedures/   │       │
│   └─────────────────┘    └─────────────────┘    └─────────────────┘       │
│                                                                            │
│   ┌─────────────────────────────────────────────────────────────┐         │
│   │                     PROCEDURAL MEMORY                        │         │
│   │  Skills, habits, rules — "how to do things"                  │         │
│   │  SOUL.md │ IDENTITY.md │ AGENTS.md │ skills/*/SKILL.md      │         │
│   └─────────────────────────────────────────────────────────────┘         │
└────────────────────────────────────────────────────────────────────────────┘
```

---

## Memory Type Decision Tree

When storing new information, ask:

```
Is it about HOW TO behave or do something?
    └─► Yes: PROCEDURAL (SOUL.md, AGENTS.md, skills/, procedures)
    └─► No: Continue...

Is it a personal experience, relationship, or preference?
    └─► Yes: EPISODIC (memory/contacts/, groups/, daily/, lessons/)
    └─► No: Continue...

Is it a fact, concept, or general knowledge?
    └─► Yes: SEMANTIC (knowledge/topics/, procedures/, research/)
    └─► No: Continue...

Is it immediately relevant to current/future sessions?
    └─► Yes: WORKING (MEMORY.md pointer/hot entry)
    └─► No: May not need storage
```

---

## System Architecture: The 5C Pipeline

```
                              ┌─────────────────┐
                              │  CONVERSATIONS  │
                              │   (realtime)    │
                              └────────┬────────┘
                                       │
                                       ▼
┌──────────────────────────────────────────────────────────────────────────┐
│                         COLLECT (bi-hourly, Haiku)                       │
│  Scans recent sessions, extracts notable content into daily files        │
└──────────────────────────────────────┬───────────────────────────────────┘
                                       │
                                       ▼
                              ┌─────────────────┐
                              │  memory/daily/  │
                              │  Raw daily logs │
                              └────────┬────────┘
                                       │
                                       ▼
┌──────────────────────────────────────────────────────────────────────────┐
│                          CURATE (daily, Sonnet)                          │
│  Routes durable content to canonical homes; MEMORY.md gets hot/index only│
└───────┬──────────┬──────────┬──────────┬──────────┬─────────────────────┘
        │          │          │          │          │
        ▼          ▼          ▼          ▼          ▼
  ┌──────────┐┌──────────┐┌──────────┐┌──────────┐┌──────────┐
  │contacts/ ││ groups/  ││ lessons/ ││knowledge/││ MEMORY.md│
  │ people   ││channels  ││ rules    ││ facts    ││ hot/index│
  └──────────┘└──────────┘└──────────┘└──────────┘└────┬─────┘
                                                        │
                                                        ▼
┌──────────────────────────────────────────────────────────────────────────┐
│                    CONSOLIDATE (weekly/semiweekly)                       │
│  MEMORY.md-only v1; byte-preserving moves; manifests; pointers           │
└──────────────────────────────────────┬───────────────────────────────────┘
                                       │ manifests/snapshots
                                       ▼
┌──────────────────────────────────────────────────────────────────────────┐
│                          COMPILE (weekly, Opus)                          │
│  Reads manifests first; extracts lessons; graduates; resolves; digests   │
└──────────────────────────────────────┬───────────────────────────────────┘
                                       │
                                       ▼
┌──────────────────────────────────────────────────────────────────────────┐
│                         CALIBRATE (monthly, Opus)                        │
│  Reviews health, lessons, procedures, foundation drift, lane balance      │
└──────────────────────────────────────────────────────────────────────────┘
```

---

## The 5C Pipeline

### Stage 0: Conversations (Realtime)

Conversations happen across channels. OpenClaw stores session transcripts automatically. This is the raw input to the system.

**No action required.** This is ephemeral platform storage.

---

### Stage 1: Collect (Bi-hourly)


**Model:** Haiku

**Purpose:** Capture notable content before it scrolls away.

Collect scans recent sessions and appends anything worth remembering to today's daily file. Append only — no routing, no organizing.

**Output:** `memory/daily/YYYY-MM-DD.md`

See `memory/.system/COLLECT.md` for full instructions.

---

### Stage 2: Curate (Daily)


**Model:** Sonnet

**Purpose:** Route raw captured material to durable canonical homes.

Curate reads the last 2-3 days of daily files and decides where durable information belongs.

| If the content is about... | Route to... |
|---|---|
| A specific person | `memory/contacts/<channel>-<id>.md` |
| A specific group | `memory/groups/<channel>-g-<name>.md` |
| A lesson or rule learned | `memory/lessons/` |
| A goal or intention | `memory/goals/` |
| An idea or brainstorm | `memory/ideas/` |
| A reminder or time-sensitive item | `memory/reminders/` |
| A factual topic | `knowledge/topics/` |
| A deep dive or analysis | `knowledge/research/` |
| A how-to or process | `knowledge/procedures/` |
| Hot/index context only | `MEMORY.md` |
| Unclear but worth keeping | `memory/notes/` |

`MEMORY.md` entries should usually be 1-3 lines and never more than 5 lines per curated item. Full detail belongs in the canonical file with a pointer from `MEMORY.md` if needed.

See `memory/.system/CURATE.md` for full instructions.

---

### Stage 3: Consolidate (Working-Memory Hygiene)


**Model:** Sonnet or stronger for v1

**Purpose:** Keep `MEMORY.md` lean without losing information.

Consolidate exists because Curate and Compile have different jobs. Curate promotes raw material into durable memory; Compile synthesizes and learns. Neither should also be responsible for routine working-memory line-count pressure.

V1 rules:

- `MEMORY.md` only
- whole-section moves only
- byte-preserving relocation
- locked pointer format
- manifest + pre-state snapshot every applied run
- source/destination hashes
- first applied run ≤100 lines moved
- normal run ≤150 lines moved
- no-op when `MEMORY.md` ≤350 lines
- tripwire when `MEMORY.md` >400 lines

Forbidden in Consolidate:

- summarization
- section splitting
- deletion
- contacts/groups/profile pruning
- foundation-file edits
- lesson extraction
- memory→knowledge graduation decisions
- contradiction resolution

See `memory/.system/CONSOLIDATE.md` for full instructions.

---

### Stage 4: Compile (Weekly)


**Model:** Opus

**Purpose:** Synthesize, learn, graduate, and summarize.

Compile begins with Consolidate manifest review. It reads moved material before extracting lessons or making graduation/pattern decisions. This prevents Consolidate from starving weekly synthesis.

Compile owns:

- manifest review and drift detection
- old daily archival and safe file organization
- lesson extraction
- maturity-based graduation to knowledge
- contradiction resolution
- recurring pattern recognition
- backlinks
- weekly digest

Compile does not own routine `MEMORY.md` size hygiene.

**Output:** `archive/digests/YYYY-MM-DD-weekly.md`

See `memory/.system/COMPILE.md` for full instructions.

---

### Stage 5: Calibrate (Monthly)


**Model:** Opus

**Purpose:** Review whether the system is healthy, balanced, and learning.

Calibrate validates the pipeline itself. It checks foundation files, lessons, missed lessons, skill/procedure gaps, Consolidate health, and stage load balance.

Calibrate owns:

- foundation drift review
- 5C lane health
- lesson validation and retirement
- missed-lesson detection
- procedure gap-fill
- skill notes recommendations
- draining Consolidate recommendations
- human-review queue

Calibrate is not routine housekeeping.

**Output:** `archive/audits/YYYY-MM-calibrate.md`

See `memory/.system/CALIBRATE.md` for full instructions.

---

## Lane Separation Summary

| Stage | Question | Output | Guardrail |
|---|---|---|---|
| Collect | What happened? | raw daily logs | append-only |
| Curate | Where does this durable item belong? | canonical memory files, staged knowledge candidates, small hot pointers | don't stuff `MEMORY.md`; don't graduate episodic material |
| Consolidate | What cooled section can leave working memory safely? | moved sections + pointers + manifests | don't synthesize |
| Compile | What did we learn this week? | lessons, graduations, patterns, digest | don't be routine janitor |
| Calibrate | Is the system working? | audit, procedure fixes, recommendations | don't be weekly cleanup |

Balanced load is a core design goal. If one stage starts doing another stage's work, narrow it before increasing its intelligence.

> **Curate routes. Consolidate relocates cooled working-memory. Compile synthesizes and learns. Calibrate audits the system.**

---

## File Structure

```
workspace/
├── MEMORY.md                       # Working memory + index (~500 line cap)
├── AGENTS.md                       # Operating guidelines
├── SOUL.md                         # Core values, personality, boundaries
├── IDENTITY.md                     # Self-concept, appearance, voice
├── USER.md                         # About the human(s)
├── TOOLS.md                        # Environment-specific notes
│
├── memory/.system/                 # 5C cognitive pipeline instructions
│   ├── HOW-IT-WORKS.md             # This document
│   ├── COLLECT.md                  # Bi-hourly session scanning
│   ├── CURATE.md                   # Daily content routing
│   ├── CONSOLIDATE.md              # Working-memory hygiene
│   ├── COMPILE.md                  # Weekly synthesis + extraction
│   └── CALIBRATE.md                # Monthly deep review
│
├── memory/                         # EPISODIC — Tier 1
│   ├── daily/                      # Raw logs
│   ├── contacts/                   # Per-person relationship memory
│   ├── groups/                     # Per-group dynamics
│   ├── lessons/                    # Behavioral rules
│   ├── reminders/                  # Time-sensitive items
│   ├── goals/                      # User intentions
│   ├── ideas/                      # Brainstorms
│   ├── lists/                      # Enumerative collections
│   └── notes/                      # Staging
│
├── knowledge/                      # SEMANTIC — Tier 2
│   ├── topics/                     # Domain knowledge
│   ├── research/                   # Deep dives
│   ├── procedures/                 # How-to reference
│   └── notes/                      # Graduated notes
│
├── archive/                        # Searchable but deprioritized
│   ├── daily/                      # Old daily files
│   ├── lessons/                    # Retired lessons
│   ├── digests/                    # Weekly digests
│   ├── audits/                     # Monthly audits
│   └── consolidate-manifests/      # Consolidate manifests/snapshots
│
├── projects/                       # Code, scripts, sandboxes
└── artifacts/                      # Generated outputs
```

---

## MEMORY.md: Working Memory + Index

`MEMORY.md` serves two functions:

### 1. Working Memory

Hot content currently relevant enough to load in main sessions without search. Keep it around ~500 lines.

### 2. Index

Map to everything else. It should point to canonical homes, not duplicate them.

**Rule:** Hard cap of 5 lines per item in `MEMORY.md`. If content needs more than 5 lines, full detail goes in the canonical file with a short pointer here.

---

## Timing Summary

| Job | Frequency | Model | Purpose |
|---|---|---|---|
| Collect | Bi-hourly | Haiku | Raw capture → daily files |
| Curate | Daily | Sonnet | Route → durable memory/knowledge |
| Consolidate | Weekly/semiweekly | Sonnet+ | `MEMORY.md` hygiene + manifests |
| Compile | Weekly (Sunday) | Opus | Synthesis, lessons, graduation, digest |
| Calibrate | Monthly (1st) | Opus | System health, procedures, foundation review |

Recommended ordering on shared days:

1. Consolidate before Curate, so it cleans accumulated prior drift rather than second-guessing fresh Curate output.
2. Compile after Consolidate, so it can review manifests and synthesize moved material.
3. Calibrate after recent Compile/digests exist, so it reviews evidence rather than raw noise.

---

## Setup & Configuration

### Use Premier Models for Structural Work

This memory system is the structural backbone of the agent's cognition. When setting up, modifying, or debugging the architecture:

> Always use your most capable available model (e.g., Opus, GPT-5.x, Sonnet 4.5+).

Lower-tier models may misunderstand routing logic, create inconsistent file structures, miss decision-tree edge cases, or introduce subtle bugs that compound over time.

### Isolated Sessions for Cron Jobs

All 5C jobs run in isolated sessions, not the main session:

| Job | Session | Model | Cadence |
|---|---|---|---|
| Collect | isolated | Haiku | Bi-hourly |
| Curate | isolated | Sonnet | Daily |
| Consolidate | isolated | Sonnet+ | Weekly/semiweekly |
| Compile | isolated | Opus | Weekly |
| Calibrate | isolated | Opus | Monthly |

**Why isolated?** Cron jobs process large amounts of content. Running them in the main session bloats context and makes conversations sluggish.

---

## Design Principles

1. **Write everything down.** Files survive session restarts; mental notes do not.
2. **Separate episodic from semantic.** Relationships and experiences are different from facts.
3. **Keep working memory lean.** `MEMORY.md` is hot/index, not archive.
4. **Preserve provenance.** Future validation depends on source and date.
5. **Balance cognitive load.** No stage should absorb all hard decisions.
6. **Match work to mover.** Mechanical work (size-based moves, content-preserving relocation) belongs with conservative movers; judgment work (lessons, graduation, contradictions) belongs with judgment models. Consolidate moves; Compile decides.
7. **Fail safe.** Use snapshots, manifests, pointers, and `trash` over destructive edits.

---

## Related Documents

| Document | Location | Purpose |
|---|---|---|
| COLLECT.md | `memory/.system/` | Bi-hourly session scanning instructions |
| CURATE.md | `memory/.system/` | Daily content routing instructions |
| CONSOLIDATE.md | `memory/.system/` | Working-memory hygiene instructions |
| COMPILE.md | `memory/.system/` | Weekly synthesis instructions |
| CALIBRATE.md | `memory/.system/` | Monthly deep review instructions |
| AGENTS.md | workspace root | Operating guidelines including session loading |
| MEMORY.md | workspace root | Working memory + structure map |

---

## Summary

The 5C memory system transforms a stateless language model into a persistent agent that:

- remembers relationships and experiences
- knows facts and procedures
- keeps working memory small enough to use
- learns from mistakes through explicit lessons
- evolves through regular system review
- avoids overloading any single cron phase

The architecture mirrors human cognition: experiences are captured, routed, consolidated out of working memory when cooled, synthesized into lessons and knowledge, and periodically audited.

The result is an AI that feels like it *knows* you — because it does.

---

*Version 3.0 — May 2026*
