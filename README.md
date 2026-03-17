# 🦞 Crustacean Cognition

**C is for Crustacean — a 4C cognitive architecture for persistent AI agents.**

---

Large language models forget everything between conversations. Every session starts from scratch — no continuity, no relationships, no learning from mistakes. For an AI agent to feel like a *someone* rather than a *something*, it needs a brain that actually remembers.

Crustacean Cognition is a file-based memory system that gives AI agents persistent memory modeled after human cognitive architecture. Four automated jobs — the **4C pipeline** — keep everything organized, curated, and evolving without manual intervention.

Built for [OpenClaw](https://openclaw.ai). Inspired by cognitive psychology. Named after lobsters, because they never stop growing.

---

## How It Works

The system implements four distinct memory types from cognitive psychology (Tulving, 1972; Squire, 2004):

```
┌────────────────────────────────────────────────────────────────────┐
│                                                                    │
│   ┌──────────────┐  ┌──────────────┐  ┌──────────────┐           │
│   │   WORKING    │  │   EPISODIC   │  │   SEMANTIC   │           │
│   │   MEMORY     │  │   MEMORY     │  │   MEMORY     │           │
│   ├──────────────┤  ├──────────────┤  ├──────────────┤           │
│   │ Current ctx  │  │ Experiences  │  │ Facts &      │           │
│   │ ~500 lines   │  │ Relationships│  │ knowledge    │           │
│   │ Hot items    │  │ Time-stamped │  │ Transferable │           │
│   └──────┬───────┘  └──────┬───────┘  └──────┬───────┘           │
│          │                 │                 │                    │
│          ▼                 ▼                 ▼                    │
│     MEMORY.md         memory/          knowledge/                │
│                    contacts, groups,   topics, research,         │
│                    lessons, ideas...   procedures...             │
│                                                                   │
│   ┌───────────────────────────────────────────────────┐          │
│   │              PROCEDURAL MEMORY                     │          │
│   │  Skills, habits, rules — "how to do things"        │          │
│   │  SOUL.md · IDENTITY.md · AGENTS.md · skills/       │          │
│   └───────────────────────────────────────────────────┘          │
│                                                                   │
└────────────────────────────────────────────────────────────────────┘
```

| Memory Type | What It Stores | Where It Lives |
|---|---|---|
| **Working** | Current context, hot items, index to everything | `MEMORY.md` (~500 line cap) |
| **Episodic** | Experiences, relationships, preferences, lessons | `memory/` (contacts, groups, daily logs...) |
| **Semantic** | Facts, concepts, procedures, reference material | `knowledge/` (topics, research, procedures...) |
| **Procedural** | Identity, values, behavioral rules, skills | Foundation files + `skills/` |

**During a conversation**, the agent loads relevant memory on demand. **After a conversation**, the 4C pipeline processes, routes, and consolidates everything automatically.

---

## The 4C Pipeline

Four automated jobs run at different cadences, each doing one thing well:

```
CONTINUOUS ──────────────────────────────────────────────────

  🪣 COLLECT (bi-hourly)          📚 CURATE (daily)
     Haiku                           Sonnet
     Scan sessions                   Route content to:
     Extract notable                 • contacts & groups
     Append to daily logs            • lessons & goals
          │                          • knowledge & topics
          ▼                          • MEMORY.md
     memory/YYYY-MM-DD.md                │
          └──────────────────────────────┘

WEEKLY ──────────────────────────────────────────────────────

  🔨 COMPILE (Sunday, Opus)
     Archive old files    │  Extract lessons
     Promote entities     │  Graduate content
     Prune stale          │  Find patterns
     Sweep loose files    │  Weekly digest

MONTHLY ─────────────────────────────────────────────────────

  🔬 CALIBRATE (1st, Opus)
     Foundation file review
     Pipeline health check
     Lessons validation
     Meta-review
```

### Collect — The Recorder 🪣

**Every 2 hours · Haiku · Isolated session**

Scans recent session transcripts and appends anything worth remembering to today's daily file. Append only — never edits, reorganizes, or routes. That's Curate's job.

What it captures: decisions, plans, people, preferences, facts, reminders, ideas, running jokes.

### Curate — The Librarian 📚

**Daily · Sonnet · Isolated session**

Reads the last 2–3 days of daily files and routes valuable content to the right long-term home using a promotion heuristic and routing table.

Routes to: contacts, groups, lessons, goals, ideas, reminders, lists, topics, research, procedures, MEMORY.md, or notes (staging).

### Compile — The Organizer & Learner 🔨

**Weekly (Sunday) · Opus · Isolated session**

A single smart pass that handles both mechanical organization and intelligent extraction. Archives old daily files, promotes overgrown entities, prunes stale content, extracts lessons from experiences, graduates mature content from memory to knowledge, detects contradictions, identifies patterns, and produces a weekly digest.

Opus because archive/prune decisions require judgment to avoid premature deletion.

### Calibrate — The Auditor 🔬

**Monthly (1st) · Opus · Isolated session**

Steps back to ask: is the system working? Am I who I should be? Reviews foundation files for identity drift, audits the 4C pipeline health, validates lessons, and proposes improvements.

---

## Directory Structure

```
workspace/
├── MEMORY.md                       # Working memory + index (~500 line cap)
├── AGENTS.md                       # Operating guidelines
├── SOUL.md                         # Core values, personality, boundaries
├── IDENTITY.md                     # Self-concept, appearance, voice
├── USER.md                         # About the human(s)
├── TOOLS.md                        # Environment-specific notes
│
├── memory/                         # EPISODIC — Tier 1, loaded contextually
│   ├── .system/                    # 4C pipeline instructions (hidden)
│   │   ├── HOW-IT-WORKS.md         # Architecture documentation
│   │   ├── COLLECT.md              # Bi-hourly session scanning
│   │   ├── CURATE.md               # Daily content routing
│   │   ├── COMPILE.md              # Weekly organization + extraction
│   │   └── CALIBRATE.md            # Monthly deep review
│   ├── YYYY-MM-DD.md               # Daily logs (raw, from Collect)
│   ├── contacts/                   # Per-person relationship memory
│   ├── groups/                     # Per-group/channel dynamics
│   ├── lessons/                    # Behavioral rules: "do X, not Y"
│   ├── reminders/                  # Time-sensitive items
│   ├── goals/                      # User goals and intentions
│   ├── ideas/                      # Raw brainstorms
│   ├── lists/                      # Enumerative collections
│   └── notes/                      # Staging ground (Curate routes elsewhere)
│
├── knowledge/                      # SEMANTIC — Tier 2, semantic search
│   ├── topics/                     # Domain knowledge, concepts, entities
│   ├── research/                   # Deep dives, analysis, findings
│   ├── procedures/                 # How-to's, workflows, processes
│   └── notes/                      # Graduated notes with lasting value
│
├── archive/                        # Tier 2 — Searchable but deprioritized
│   ├── daily/                      # Old daily files, by month
│   ├── lessons/                    # Retired/outdated lessons
│   ├── digests/                    # Weekly compile digests
│   └── audits/                     # Monthly calibrate reports
│
├── projects/                       # Tier 3 — Code, scripts, sandboxes
│
└── artifacts/                      # Tier 3 — Generated outputs
    ├── images/
    ├── diagrams/
    └── exports/
```

### Retrieval Tiers

| Tier | What | When Loaded |
|---|---|---|
| **1 — Memory** | Episodic content (contacts, lessons, daily logs) | Contextually — right person/group/topic gets loaded |
| **2 — Knowledge** | Semantic content (topics, procedures, research) | On demand via semantic search |
| **2 — Archive** | Old content, digests, audit reports | Searchable but deprioritized |
| **3 — Projects/Artifacts** | Code, generated outputs | Only by explicit reference |

---

## Key Design Decisions

**Memory vs Knowledge** — The split mirrors episodic vs semantic memory in humans. `memory/` holds *what happened to me* (relationships, preferences, experiences). `knowledge/` holds *what is true* (facts, procedures, reference material). Content graduates from memory to knowledge when it becomes stable, factual, and transferable.

**Lessons in memory/, not knowledge/** — Lessons are behavioral: "remember to do X, not Y." They're personal and experiential. Procedures are in `knowledge/` because they're transferable reference: "how to do X." Clean split.

**Single weekly Compile pass** — The old system split this into two jobs (mechanical + intelligent). We combined them because the model already has full workspace context loaded — splitting means loading it twice for no benefit. Once a week on a capable model is negligible cost.

**Daily files in memory/ root** — Not in a `daily/` subdirectory. The session-memory hook writes there by default, and fighting the platform with sweep steps is a band-aid. Dated filenames (`2026-03-16-*.md`) are visually distinct from the subdirectories. Compile archives them after 30 days.

**No `entities/` directory** — Merged into `knowledge/topics/`. The entity/topic boundary is subjective and creates routing confusion. A topic file can cover a company, a place, or a concept equally well.

**Session-memory hook compatibility** — OpenClaw's bundled `session-memory` hook writes a short session summary to `memory/` when `/new` is issued (e.g., `memory/2026-03-16-api-design.md`). These are redundant with what Collect already extracts from full session transcripts. The architecture handles this intentionally: Collect ignores them (it reads the richer source), and Compile sweeps them into `archive/daily/` during the weekly pass. No conflict, no duplicate processing — just let them accumulate and Compile cleans up.

---

## Session Loading

Not everything loads every time. The agent picks what it needs based on context:

| Context | What to Load |
|---|---|
| **Every session** | SOUL.md, IDENTITY.md, USER.md, today's + yesterday's daily file |
| **Main session** (direct chat with owner) | + MEMORY.md |
| **DM with specific person** | + their contact file |
| **Group chat** | + the group's dynamics file |
| **Topic comes up** | Lazy-load relevant knowledge/topics/ file |
| **Uncertain or error** | Check memory/lessons/ for relevant rules |

---

## Design Principles

1. **Write everything down.** "Mental notes" don't survive session restarts. Files do.
2. **Separate episodic from semantic.** Relationships ≠ facts. Store them separately.
3. **Promote aggressively.** Don't let MEMORY.md become a dumping ground.
4. **Include provenance.** Record *when* and *why* something was learned so it can be validated later.
5. **Automate the boring parts.** Collect, Curate, and Tidy run automatically. Humans focus on interesting decisions.
6. **Review regularly.** Weekly Compile keeps things pruned. Monthly Calibrate ensures the system evolves.
7. **Fail safe.** `trash` > `rm`. Capture more than necessary — easier to prune than recover.

---

## Theoretical Foundation

This architecture draws from established cognitive psychology research:

- **Tulving (1972)** — Distinction between episodic and semantic memory
- **Baddeley & Hitch (1974)** — Working memory model
- **Squire (2004)** — Taxonomy of long-term memory systems
- **ICLR 2026 MemAgents Workshop** — Emerging research on memory systems for AI agents

Reference implementation: [ALucek/agentic-memory](https://github.com/ALucek/agentic-memory) — a clean demonstration of the four-system architecture applied to LLM agents.

> *"Memory is not a single faculty but a collection of distinct systems that work together."*
> — Endel Tulving

---

## Getting Started

This is an architecture, not a library. To adopt it:

1. **Create the directory structure** in your agent's workspace
2. **Copy the pipeline docs** from `pipeline/` to your `memory/.system/`
3. **Set up cron jobs** for each of the 4C stages (see pipeline docs for cadences and model recommendations)
4. **Adapt MEMORY.md** as your working memory index
5. **Let it run** — the system is designed to be self-maintaining after initial setup

Built for [OpenClaw](https://openclaw.ai), but the architecture is platform-agnostic. Any agent framework with file access and scheduled jobs can implement it.

---

## Pipeline Documentation

Detailed instructions for each stage live in [`pipeline/`](./pipeline/):

| Document | Purpose |
|---|---|
| [COLLECT.md](./pipeline/COLLECT.md) | Bi-hourly session scanning — what to extract, format, boundaries |
| [CURATE.md](./pipeline/CURATE.md) | Daily content routing — promotion heuristic, routing table, deduplication |
| [COMPILE.md](./pipeline/COMPILE.md) | Weekly organization + extraction — archival, promotion, lessons, graduation |
| [CALIBRATE.md](./pipeline/CALIBRATE.md) | Monthly deep review — foundation files, pipeline health, lessons validation |
| [HOW-IT-WORKS.md](./pipeline/HOW-IT-WORKS.md) | Full architecture documentation with diagrams and academic grounding |

---

## Why "Crustacean"?

Because lobsters never stop growing. They molt — shed their old shell when it gets too tight, grow a new one, and keep going. That's what this memory system does. Old content gets archived, new structure forms, and the agent keeps evolving.

Also, the 4C's: **C**ollect, **C**urate, **C**ompile, **C**alibrate. C is for Crustacean. 🦀

---

*Built with claws by [clawSean](https://github.com/clawSean) · March 2026*
