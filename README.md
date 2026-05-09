# 🦞 Crustacean Cognition

**C is for Crustacean — a 5C cognitive architecture for persistent AI agents.**

---

Large language models forget everything between conversations. Every session starts from scratch — no continuity, no relationships, no learning from mistakes. For an AI agent to feel like a *someone* rather than a *something*, it needs a brain that actually remembers.

Crustacean Cognition is a file-based memory system that gives AI agents persistent memory modeled after human cognitive architecture. Five automated jobs — the **5C pipeline** — keep memory captured, routed, lean, synthesized, and improving without turning any single phase into an unreliable mega-job.

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
│   │ Hot/index    │  │ Time-stamped │  │ Transferable │           │
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

**During a conversation**, the agent loads relevant memory on demand. **After a conversation**, the 5C pipeline processes, routes, consolidates, synthesizes, and audits memory automatically.

---

## The 5C Pipeline

Five automated jobs run at different cadences, each with a sharply defined lane:

```
CONTINUOUS / DAILY ───────────────────────────────────────────

  🪣 COLLECT (bi-hourly)          📚 CURATE (daily)
     Haiku                           Sonnet
     Raw capture                     Durable routing
     Append-only                     Full detail → canonical homes
          │                          MEMORY.md → hot/index only
          ▼                                │
     memory/daily/                         ▼
                                  contacts/groups/lessons/
                                  goals/knowledge/notes/MEMORY.md

WORKING-MEMORY HYGIENE ───────────────────────────────────────

  🧹 CONSOLIDATE (weekly/semiweekly, before Curate when shared day)
     Sonnet+ / conservative
     MEMORY.md-only v1
     Byte-preserving whole-section moves
     Manifests + snapshots + pointers

WEEKLY SYNTHESIS ─────────────────────────────────────────────

  🔨 COMPILE (Sunday, Opus)
     Read Consolidate manifests first
     Extract lessons
     Graduate mature content
     Resolve contradictions
     Find patterns
     Weekly digest

MONTHLY LEARNING / SYSTEM REVIEW ─────────────────────────────

  🔬 CALIBRATE (1st, Opus)
     Foundation review
     Pipeline load-balance check
     Lesson validation
     Procedure gap-fill
     Consolidate health review
```

### Collect — The Recorder 🪣

**Every 2 hours · Haiku · Isolated session**

Scans recent session transcripts and appends anything worth remembering to today's daily file. Append only — never edits, reorganizes, or routes. That's Curate's job.

### Curate — The Librarian 📚

**Daily · Sonnet · Isolated session**

Reads the last 2-3 days of daily files and routes valuable content to the right canonical home. Curate promotes raw experience into durable memory, but `MEMORY.md` is only for hot/index entries. Full detail belongs in contacts, groups, lessons, goals, reminders, knowledge, or notes/staging.

### Consolidate — The Working-Memory Steward 🧹

**Weekly or semiweekly · Sonnet+ · Isolated session**

Keeps `MEMORY.md` lean without stealing Compile's synthesis role. V1 is conservative: `MEMORY.md` only, whole-section byte-preserving relocation, locked pointers, manifests, pre-state snapshots, and hashes. No summarization, no section splitting, no deletion, no lesson extraction, no memory→knowledge graduation decisions.

Consolidate is not the opposite of Curate. Curate promotes raw experience into durable memory; Consolidate keeps the working-memory index from becoming a warehouse.

### Compile — The Organizer & Learner 🔨

**Weekly (Sunday) · Opus · Isolated session**

Begins with Consolidate manifest review so moved material remains visible. Then performs judgment-heavy synthesis: lessons, graduation, contradiction resolution, recurring patterns, backlinks, and a weekly digest. Compile does not own routine `MEMORY.md` line-count hygiene.

### Calibrate — The Auditor 🔬

**Monthly (1st) · Opus · Isolated session**

Reviews whether the system is working and whether lanes remain balanced. Validates lessons, finds missed lessons, fills procedure gaps, reviews foundation drift, drains Consolidate recommendations, and tunes the 5C system. It is not routine housekeeping.

---

## Lane Separation

| Stage | Primary Question | Owns | Must Not Become |
|---|---|---|---|
| Collect | "What happened?" | raw capture into daily logs | router/summarizer |
| Curate | "Where should this durable item live?" | daily routing to canonical homes | `MEMORY.md` dumping ground |
| Consolidate | "What cooled content can leave working memory safely?" | conservative `MEMORY.md` hygiene | synthesizer/pruner |
| Compile | "What did we learn this week?" | lessons, graduation, contradiction resolution, patterns, digest | routine janitor or daily router |
| Calibrate | "Is the system learning and balanced?" | process/foundation/lesson review | weekly cleanup pass |

The design goal is balanced difficulty: no phase should carry all capture, routing, cleanup, synthesis, and system learning at once.

> **Curate routes. Consolidate relocates cooled working-memory. Compile synthesizes and learns. Calibrate audits the system.**

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
│   ├── .system/                    # 5C pipeline instructions (hidden)
│   │   ├── HOW-IT-WORKS.md         # Architecture documentation
│   │   ├── COLLECT.md              # Bi-hourly session scanning
│   │   ├── CURATE.md               # Daily content routing
│   │   ├── CONSOLIDATE.md          # Working-memory hygiene
│   │   ├── COMPILE.md              # Weekly synthesis + extraction
│   │   └── CALIBRATE.md            # Monthly deep review
│   ├── daily/                      # Daily logs (raw, from Collect)
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
│   ├── audits/                     # Monthly calibrate reports
│   └── consolidate-manifests/      # Consolidate manifests/snapshots/recs
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

**How tiers affect retrieval:** The `memory/` and `knowledge/` split isn't just organizational — it determines how content gets found. Files in `memory/` are automatically included in OpenClaw's native memory search on every message intake. Files in `knowledge/` are searched via semantic vector search when the agent needs deeper context. If you want something to surface automatically, put it in `memory/`. If you want it findable on demand, put it in `knowledge/`. For local semantic search over `knowledge/`, see [lobsearch](https://github.com/clawSean/lobsearch) — a drop-in Ollama + ChromaDB skill that runs entirely on your machine.

---

## Key Design Decisions

**Memory vs Knowledge** — The split mirrors episodic vs semantic memory in humans. `memory/` holds *what happened to me* (relationships, preferences, experiences). `knowledge/` holds *what is true* (facts, procedures, reference material). Content graduates from memory to knowledge when it becomes stable, factual, and transferable.

**Lessons in memory/, not knowledge/** — Lessons are behavioral: "remember to do X, not Y." They're personal and experiential. Procedures are in `knowledge/` because they're transferable reference: "how to do X." Clean split.

**`MEMORY.md` is hot/index, not warehouse** — `MEMORY.md` should stay small enough to load in main sessions. Curate may write hot context and pointers there, but full detail belongs in canonical files. Consolidate exists because working memory needs an explicit steward.

**Consolidate separate from Compile** — Routine working-memory hygiene and weekly synthesis are different jobs. Consolidate moves cooled whole sections conservatively; Compile interprets, extracts lessons, graduates content, and notices patterns. This keeps both jobs dependable.

**Compile reads Consolidate manifests first** — Consolidate is safe only because Compile sees what moved. Manifests, hashes, and pre-state snapshots prevent telephone-game drift.

**No `entities/` directory** — Merged into `knowledge/topics/`. The entity/topic boundary is subjective and creates routing confusion. A topic file can cover a company, a place, or a concept equally well.

**Session-memory hook compatibility** — OpenClaw's bundled `session-memory` hook writes short session summaries to `memory/` when `/new` is issued. These are redundant with what Collect extracts from full session transcripts. The architecture handles this intentionally: Collect ignores them, and Compile sweeps them into `archive/daily/` during the weekly pass.

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
3. **Keep working memory lean.** `MEMORY.md` is hot context and index, not the archive.
4. **Route full detail to canonical homes.** Contacts, groups, lessons, goals, knowledge, notes.
5. **Preserve provenance.** Record when and why something was learned so it can be validated later.
6. **Balance the load.** No single phase should be capture + routing + cleanup + synthesis + audit.
7. **Match work to mover.** Mechanical work (size-based moves, content-preserving relocation) belongs with conservative movers; judgment work (lessons, graduation, contradictions) belongs with judgment models.
8. **Fail safe.** `trash` > `rm`; pointers > deletion; manifests > mystery.

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

1. Create the directory structure in your agent's workspace.
2. Copy the pipeline docs from `pipeline/` to your `memory/.system/`.
3. Set up cron jobs for each of the 5C stages.
4. Adapt `MEMORY.md` as your working-memory index.
5. Let it run, then inspect manifests/digests/audits until trust is earned.

Built for [OpenClaw](https://openclaw.ai), but the architecture is platform-agnostic. Any agent framework with file access and scheduled jobs can implement it.

---

## Pipeline Documentation

Detailed instructions for each stage live in [`pipeline/`](./pipeline/):

| Document | Purpose |
|---|---|
| [COLLECT.md](./pipeline/COLLECT.md) | Bi-hourly session scanning — what to extract, format, boundaries |
| [CURATE.md](./pipeline/CURATE.md) | Daily content routing — promotion heuristic, routing table, `MEMORY.md` limits |
| [CONSOLIDATE.md](./pipeline/CONSOLIDATE.md) | Working-memory hygiene — conservative relocation, manifests, pointers |
| [COMPILE.md](./pipeline/COMPILE.md) | Weekly synthesis — manifest review, lessons, graduation, patterns, digest |
| [CALIBRATE.md](./pipeline/CALIBRATE.md) | Monthly deep review — foundation files, pipeline health, lessons validation |
| [HOW-IT-WORKS.md](./pipeline/HOW-IT-WORKS.md) | Full architecture documentation with diagrams and academic grounding |

---

## Future: Correlate (6th C)

> *Speculative — not yet implemented.*

**Correlate** is a potential future stage focused on graphically organized knowledge — building and maintaining a graph of relationships between entities, topics, lessons, and events. Where Compile synthesizes linearly (lessons, graduation, digest), Correlate would synthesize structurally: "these three things are connected, this concept links to that person, this lesson reinforces that procedure."

Possible outputs: a knowledge graph, entity relationship maps, backlink networks, or structured topic clusters that semantic search alone can't surface. The idea is that pattern recognition across the whole knowledge base requires a different representation than flat markdown files.

File this under "when the flat file system starts feeling like the bottleneck."

---

## Why "Crustacean"?

Because lobsters never stop growing. They molt — shed their old shell when it gets too tight, grow a new one, and keep going. That's what this memory system does. Old content gets archived, new structure forms, and the agent keeps evolving.

Also, the 5C's (and maybe someday 6): **C**ollect, **C**urate, **C**onsolidate, **C**ompile, **C**alibrate — and **C**orrelate when the graph beckons. C is for Crustacean. 🦀

---

*Built with claws by [clawSean](https://github.com/clawSean) · March 2026*
