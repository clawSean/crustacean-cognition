# The OpenClaw Memory System

**A cognitive architecture for persistent AI agents**

---

## The Problem

Large language models have no persistent memory. Each conversation starts fresh. This creates a fundamental limitation: the AI cannot learn from experience, remember relationships, or accumulate knowledge over time.

For an AI assistant to feel like a *someone* rather than a *something*, it needs:

- **Continuity** — remembering what happened yesterday, last week, last month
- **Relationships** — knowing who people are, how they communicate, what they care about
- **Learning** — extracting lessons from mistakes and successes
- **Knowledge** — accumulating facts and expertise over time

This document describes a complete memory system that solves these problems.

---

## Theoretical Foundation: The Four Memory Systems

Cognitive psychology identifies four distinct memory systems in humans. This architecture implements all four:

```
┌────────────────────────────────────────────────────────────────────────────┐
│                                                                            │
│                        THE FOUR MEMORY SYSTEMS                             │
│                    (Tulving, 1972; Squire, 2004)                           │
│                                                                            │
├────────────────────────────────────────────────────────────────────────────┤
│                                                                            │
│   ┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐       │
│   │    WORKING      │    │    EPISODIC     │    │    SEMANTIC     │       │
│   │    MEMORY       │    │    MEMORY       │    │    MEMORY       │       │
│   ├─────────────────┤    ├─────────────────┤    ├─────────────────┤       │
│   │ Current context │    │ Personal events │    │ Facts & concepts│       │
│   │ Limited capacity│    │ Autobiographical│    │ General knowledge       │
│   │ Immediate focus │    │ Time-stamped    │    │ Context-free    │       │
│   └─────────────────┘    └─────────────────┘    └─────────────────┘       │
│           │                      │                      │                 │
│           ▼                      ▼                      ▼                 │
│   ┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐       │
│   │   MEMORY.md     │    │    memory/      │    │   knowledge/    │       │
│   │   (~500 lines)  │    │   daily/        │    │   topics/       │       │
│   │   Session ctx   │    │   contacts/     │    │   research/     │       │
│   │                 │    │   groups/       │    │   procedures/   │       │
│   └─────────────────┘    └─────────────────┘    └─────────────────┘       │
│                                                                            │
│   ┌─────────────────────────────────────────────────────────────┐         │
│   │                     PROCEDURAL MEMORY                        │         │
│   ├─────────────────────────────────────────────────────────────┤         │
│   │  Skills, habits, rules — "how to do things"                  │         │
│   │  Implicit knowledge that guides behavior                     │         │
│   └─────────────────────────────────────────────────────────────┘         │
│                                  │                                         │
│                                  ▼                                         │
│   ┌─────────────────────────────────────────────────────────────┐         │
│   │  SOUL.md │ IDENTITY.md │ AGENTS.md │ skills/*/SKILL.md      │         │
│   └─────────────────────────────────────────────────────────────┘         │
│                                                                            │
└────────────────────────────────────────────────────────────────────────────┘
```

### 1. Working Memory
**Psychology:** The cognitive system that holds information temporarily for processing. Limited capacity (~7 items), immediate focus, constantly updated.

**Implementation:** `MEMORY.md` + current session context
- Capped at ~500 lines to respect capacity limits
- Contains "hot" items needed for immediate tasks
- Serves as index to retrieve from other memory systems
- Updated continuously during conversations

### 2. Episodic Memory
**Psychology:** Autobiographical memory for personal experiences and events. Time-stamped, contextual, answers "what happened to me."

**Implementation:** `memory/` directory
- `daily/` — Raw experience logs, timestamped
- `contacts/` — Relationship histories with specific people
- `groups/` — Experiences within specific groups
- `lessons/` — Behavioral rules learned from experience
- `reminders/` — Time-sensitive items
- `goals/` — User intentions and plans
- `ideas/` — Creative moments captured in time
- `lists/` — Enumerative collections
- `notes/` — Staging ground for uncategorized content

### 3. Semantic Memory
**Psychology:** General world knowledge independent of personal experience. Facts, concepts, meanings — answers "what is true."

**Implementation:** `knowledge/` directory
- `topics/` — How things work, what things are (APIs, protocols, companies, places)
- `research/` — Deep dives, analysis, findings
- `procedures/` — How-to's, workflows, processes, reference material
- `notes/` — Graduated notes with lasting knowledge value

### 4. Procedural Memory
**Psychology:** Implicit memory for skills and habits. "How to do things" — often automatic, hard to verbalize explicitly.

**Implementation:** Foundation files + skills
- `SOUL.md` — Core values, personality, boundaries
- `IDENTITY.md` — Self-concept, appearance, voice
- `AGENTS.md` — Operating procedures, behavioral rules
- `skills/*/SKILL.md` — Specific capabilities and how to use them

---

### The Four Systems Working Together

```
┌──────────────────────────────────────────────────────────────────┐
│                         CONVERSATION                              │
│                              │                                    │
│    ┌─────────────────────────┼─────────────────────────┐         │
│    │                         ▼                         │         │
│    │  ┌─────────────────────────────────────────────┐  │         │
│    │  │            WORKING MEMORY                    │  │         │
│    │  │         (active processing)                  │  │         │
│    │  └──────┬──────────┬──────────┬────────────────┘  │         │
│    │         │          │          │                   │         │
│    │    ┌────▼────┐ ┌───▼───┐ ┌────▼─────┐            │         │
│    │    │EPISODIC │ │SEMANTIC│ │PROCEDURAL│            │         │
│    │    │"What    │ │"What   │ │"How do   │            │         │
│    │    │happened"│ │is true"│ │I behave" │            │         │
│    │    └─────────┘ └────────┘ └──────────┘            │         │
│    │                                                   │         │
│    │         RETRIEVAL ←──────── ENCODING             │         │
│    │     (load on demand)    (store after session)    │         │
│    └───────────────────────────────────────────────────┘         │
└──────────────────────────────────────────────────────────────────┘
```

**During a conversation:**
1. **Procedural memory** shapes *how* I respond (personality, rules, skills)
2. **Working memory** holds the current context and task
3. **Episodic memory** is queried for relevant past experiences ("Have I talked to this person before?")
4. **Semantic memory** is queried for relevant facts ("How does this API work?")

**After a conversation:**
1. Notable content flows to **episodic memory** (daily logs, contact/group updates)
2. Stable facts graduate to **semantic memory** (knowledge files)
3. Learned rules update **episodic memory** (lessons) or **procedural memory** (AGENTS.md)
4. **Working memory** is pruned to stay within capacity

---

### Memory Type Decision Tree

When storing new information, ask:

```
Is it about HOW TO behave or do something?
    └─► Yes: PROCEDURAL (SOUL.md, AGENTS.md, skills/)
    └─► No: Continue...

Is it a personal experience, relationship, or preference?
    └─► Yes: EPISODIC (memory/contacts/, groups/, daily/, lessons/)
    └─► No: Continue...

Is it a fact, concept, or general knowledge?
    └─► Yes: SEMANTIC (knowledge/topics/, procedures/, research/)
    └─► No: Continue...

Is it immediately relevant to the current task?
    └─► Yes: WORKING (MEMORY.md, session context)
    └─► No: May not need storage
```

---

### Academic Grounding

This architecture draws from established research:

- **Tulving (1972)** — Distinction between episodic and semantic memory
- **Baddeley & Hitch (1974)** — Working memory model
- **Squire (2004)** — Taxonomy of long-term memory systems
- **ICLR 2026 MemAgents Workshop** — Emerging research on memory systems for AI agents

Reference implementation: [ALucek/agentic-memory](https://github.com/ALucek/agentic-memory) — A clean demonstration of the four-system architecture applied to LLM agents.

> *"Memory is not a single faculty but a collection of distinct systems that work together."*
> — Endel Tulving

---

## System Architecture: The 4C Pipeline

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
│  Routes valuable content to appropriate long-term storage                │
└───────┬──────────┬──────────┬──────────┬──────────┬─────────────────────┘
        │          │          │          │          │
        ▼          ▼          ▼          ▼          ▼
  ┌──────────┐┌──────────┐┌──────────┐┌──────────┐┌──────────┐
  │ memory/  ││ memory/  ││ memory/  ││knowledge/││ MEMORY.md│
  │contacts/ ││ groups/  ││ lessons/ ││ topics/  ││ (working │
  │(people)  ││(channels)││ (rules)  ││ (facts)  ││  memory) │
  └──────────┘└──────────┘└──────────┘└──────────┘└──────────┘
        │          │          │          │          │
        └──────────┴──────────┴──────────┴──────────┘
                                       │
        ┌──────────────────────────────┘
        │
        ▼
┌─────────────────┐                              ┌─────────────────┐
│     COMPILE     │                              │    CALIBRATE    │
│    (weekly)     │                              │    (monthly)    │
│     Opus        │                              │     Opus        │
├─────────────────┤                              ├─────────────────┤
│ • Archive old   │                              │ • Foundation    │
│ • Promote       │                              │   file review   │
│ • Prune stale   │                              │ • Pipeline      │
│ • Extract       │                              │   health check  │
│   lessons       │                              │ • Lessons       │
│ • Graduate      │                              │   validation    │
│   content       │                              │ • Security scan │
│ • Find patterns │                              │ • Meta-review   │
│ • Weekly digest │                              │                 │
└─────────────────┘                              └─────────────────┘
        │                                                │
        └────────────────────────────────────────────────┘
                              │
                       Organized, learned,
                          improved
```

---

## The 4C Pipeline

### Stage 0: Conversations (Realtime)

Conversations happen across channels — Telegram DMs, group chats, etc. OpenClaw stores session transcripts automatically. This is ephemeral storage managed by the platform.

**No action required.** This is the raw input to the system.

---

### Stage 1: Collect (Bi-hourly)

**Model:** Haiku  
**Purpose:** Capture notable content before it scrolls away.

Collect scans recent sessions (2-day lookback) and appends anything worth
remembering to today's daily file. Append only — no routing, no organizing.

**Output:** Appends to `memory/daily/YYYY-MM-DD.md`

**Why bi-hourly?** Frequent enough to catch everything, infrequent enough to batch efficiently. Raw session data is preserved by OpenClaw regardless.

See `memory/.system/COLLECT.md` for full instructions.

---

### Stage 2: Curate (Daily)

**Model:** Sonnet  
**Purpose:** Route content to the right long-term home.

Curate reads the last 2-3 days of daily files (overlapping window) and makes routing decisions using a promotion heuristic and decision tree.

| If the content is about... | Route to... |
|---|---|
| A specific person | `memory/contacts/<channel>-<id>.md` |
| A specific group | `memory/groups/<channel>-g-<name>.md` |
| A lesson or rule learned | `memory/lessons/` |
| A goal or intention | `memory/goals/` |
| An idea or brainstorm | `memory/ideas/` |
| A reminder or time-sensitive | `memory/reminders/` |
| An enumerative list | `memory/lists/` |
| A factual topic (how X works) | `knowledge/topics/` |
| A deep dive or analysis | `knowledge/research/` |
| A how-to or process | `knowledge/procedures/` |
| Something hot/current | `MEMORY.md` |
| Catch-all | `memory/notes/` (staging) |

See `memory/.system/CURATE.md` for full instructions.

---

### Stage 3: Compile (Weekly)

**Model:** Opus  
**Purpose:** Organize, extract, graduate, and summarize.

Compile is a single smart weekly pass that handles both mechanical organization (archive, promote, prune) and intelligent work (extract lessons, graduate content, find patterns, add backlinks). These are combined because the model already has full workspace context — splitting into two jobs means loading it twice for no benefit.

**Mechanical:** Archive old dailies, promote overgrown entities, prune stale content  
**Intelligent:** Extract lessons, graduate mature content to knowledge/, detect contradictions, identify patterns

**Output:** `archive/digests/YYYY-MM-DD-weekly.md`

See `memory/.system/COMPILE.md` for full instructions.

---

### Stage 4: Calibrate (Monthly)

**Model:** Opus  
**Purpose:** Deep reflection on system health and agent evolution.

Monthly review covering foundation files, pipeline health, lessons validation, skill feedback, security scan, and meta-review of the calibration process itself.

**Output:** `archive/audits/YYYY-MM-calibrate.md` + proposed updates

See `memory/.system/CALIBRATE.md` for full instructions.

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
├── HEARTBEAT.md                    # Heartbeat config
│
├── memory/.system/                     # 4C cognitive pipeline instructions (hidden)
│   ├── HOW-IT-WORKS.md             # This document
│   ├── COLLECT.md                  # Bi-hourly session scanning
│   ├── CURATE.md                   # Daily content routing
│   ├── COMPILE.md                  # Weekly organization + extraction
│   └── CALIBRATE.md                # Monthly deep review
│
├── routines/                       # Operational cron job guides
│   └── tidy.md                     # Misplaced file detection
│
├── memory/                         # EPISODIC — Tier 1, loaded contextually
│   ├── daily/                      # Raw logs (current day always loaded)
│   ├── contacts/                   # Per-person relationship memory
│   ├── groups/                     # Per-group dynamics
│   ├── lessons/                    # Behavioral rules: "do X, not Y"
│   ├── reminders/                  # Time-sensitive items
│   ├── goals/                      # User goals and intentions
│   ├── ideas/                      # Raw brainstorms
│   ├── lists/                      # Enumerative collections
│   └── notes/                      # Staging ground for uncategorized content
│
├── knowledge/                      # SEMANTIC — Tier 2, semantic search
│   ├── topics/                     # Domain knowledge, concepts, entities
│   ├── research/                   # Deep dives, analysis, findings
│   ├── procedures/                 # How-to's, workflows, processes
│   └── notes/                      # Graduated notes with lasting value
│
├── archive/                        # Tier 2, searchable but deprioritized
│   ├── daily/                      # Old daily files, by month
│   ├── lessons/                    # Retired/outdated lessons
│   ├── digests/                    # Weekly compile digests
│   ├── audits/                     # Monthly calibrate reports
│   └── ...                         # Subdirectories by origin
│
├── projects/                       # Tier 3 — Code, scripts, sandboxes
│
└── artifacts/                      # Tier 3 — Generated outputs
    ├── images/
    ├── diagrams/
    └── exports/
```

---

## MEMORY.md: The Working Memory

MEMORY.md serves two critical functions:

### 1. Working Memory
The "hot" content that's currently relevant. Things that should be immediately accessible without searching. Capped at ~500 lines to keep context windows manageable.

### 2. Index
The map to everything else. When you need to find something, MEMORY.md tells you where it lives. The "Memory Structure Map" section documents all file locations.

**Entity Promotion Rule:** When any entity in MEMORY.md exceeds 10 lines or 5 mentions, it gets promoted to its own dedicated file. This keeps working memory lean.

---

## Session Loading

Different contexts require different memory:

| Context | What to Load |
|---------|--------------|
| **Every session** | SOUL.md, IDENTITY.md, USER.md, daily files (today + yesterday) |
| **Main session** (direct chat with owner) | + MEMORY.md |
| **DM with specific person** | + their `memory/contacts/` file |
| **Group chat** | + the `memory/groups/` file |
| **Topic comes up** | Lazy-load relevant `knowledge/topics/` file |
| **Using a skill** | Check `skills/<skill>/NOTES.md` for usage tips |
| **Uncertain or error** | Check `memory/lessons/` for relevant rules |

This ensures the agent has relationship context when talking to someone specific, without loading everything every time.

---

## The Lessons System

Lessons are the distillation of experience into actionable behavioral rules. They live in `memory/lessons/` (Tier 1 — always accessible).

**Structure:**
```markdown
## Lesson Title
**Learned:** [date] (incident reference)
**Context:** What happened
**Rule:** What to do / not do
```

Lessons start in a single `lessons.md` file. If they grow large, split by domain (security, communication, technical, social, skills) into separate files within the folder.

**Key distinction:** `memory/lessons/` = "remember to do/avoid X" (behavioral). `knowledge/procedures/` = "how to do X" (reference, transferable).

**Why provenance matters:** Including when and why a lesson was learned allows Calibrate to validate them. Circumstances change; lessons may become obsolete.

---

## Timing Summary

| Job | Frequency | Model | Purpose |
|-----|-----------|-------|---------|
| Collect | Bi-hourly | Haiku | Raw capture → daily files |
| Curate | Daily | Sonnet | Route → long-term storage |
| Compile | Weekly (Sunday) | Opus | Organize, extract, graduate, digest |
| Calibrate | Monthly (1st) | Opus | System health, review, security |
| Tidy | Weekly or as-needed | Haiku | Misplaced file detection |

### Complete Job System

```
CONTINUOUS (keeps things flowing):
─────────────────────────────────────────────────────────────────
  COLLECT (bi-hourly)            CURATE (daily)
  Haiku                          Sonnet
       │                              │
  Scan sessions               Route content to:
  Extract notable             • memory/contacts/
  Append to daily/            • memory/groups/
       │                      • memory/lessons/
       ▼                      • knowledge/topics/
  memory/daily/               • MEMORY.md
       └──────────────────────────────┘

WEEKLY (keeps things organized & learned):
─────────────────────────────────────────────────────────────────
  COMPILE (Opus)
       │
  Archive old files
  Promote entities
  Prune stale
  Extract lessons
  Graduate content
  Find patterns
  Weekly digest

MONTHLY (keeps things healthy & evolving):
─────────────────────────────────────────────────────────────────
  CALIBRATE (Opus)
       │
  Foundation review
  Pipeline health check
  Lessons validation
  Security scan
  Meta-review
```

---

## Setup & Configuration

### Use Premier Models for Structural Work

This memory system is the **structural backbone** of the agent's cognition. When setting up, modifying, or debugging the architecture:

> Always use your most capable model available (e.g., Opus, GPT-5.x, Sonnet 4.5+)

Lower-tier models may:
- Misunderstand the routing logic
- Create inconsistent file structures  
- Miss edge cases in the decision trees
- Introduce subtle bugs that compound over time

**Rule of thumb:** If you're touching foundation files (`SOUL.md`, `AGENTS.md`, `MEMORY.md`) or pipeline files (`memory/.system/*`), use a premier model.

---

### DM Session Isolation (`dmScope`)

The `contacts/` memory system assumes each person can have personalized context loaded. To fully leverage this:

| `dmScope` Setting | Behavior | Memory System Fit |
|-------------------|----------|-------------------|
| `main` (default) | All DMs share one session | Cross-user context leakage risk |
| `per-channel-peer` | Each DM sender gets isolated session | Perfect for contacts/ memory |
| `per-account-channel-peer` | Isolated + multi-account aware | For multi-account setups |

**For personal assistants** (single owner, trusted users): `main` is acceptable if you want unified context.

**For shared/multi-user scenarios**: `per-channel-peer` is strongly recommended.

---

### Isolated Sessions for Cron Jobs

All 4C pipeline jobs run in **isolated sessions**, not the main session:

| Job | Session | Model | Cadence |
|-----|---------|-------|---------|
| Collect | `isolated` | Haiku | Bi-hourly |
| Curate | `isolated` | Sonnet | Daily |
| Compile | `isolated` | Opus | Weekly (Sunday) |
| Calibrate | `isolated` | Opus | Monthly (1st) |
| Tidy | `isolated` | Haiku | Weekly or as-needed |

**Why isolated?** Cron jobs process large amounts of content. Running them in main session bloats context and makes conversations sluggish. Isolated sessions start fresh, process content, write to files, and exit cleanly.

---

## Design Principles

### 1. Write Everything Down
"Mental notes" don't survive session restarts. If it's worth remembering, write it to a file.

### 2. Separate Episodic from Semantic
Relationships and experiences are different from facts and rules. Store them separately.

### 3. Promote Aggressively
Don't let MEMORY.md become a dumping ground. Promote entities to dedicated files early.

### 4. Include Provenance
Always record when something was learned and why. This enables future validation.

### 5. Automate the Boring Parts
Collect, Curate, and Tidy run automatically. Humans focus on the interesting decisions.

### 6. Review Regularly
Weekly Compile keeps things pruned and learned. Monthly Calibrate ensures the system evolves.

### 7. Fail Safe
Use `trash` instead of `rm`. Capture more than necessary — easier to prune than recover.

---

## Related Documents

| Document | Location | Purpose |
|----------|----------|---------|
| COLLECT.md | `memory/.system/` | Bi-hourly session scanning instructions |
| CURATE.md | `memory/.system/` | Daily content routing instructions |
| COMPILE.md | `memory/.system/` | Weekly organization + extraction instructions |
| CALIBRATE.md | `memory/.system/` | Monthly deep review instructions |
| AGENTS.md | workspace root | Operating guidelines including session loading |
| MEMORY.md | workspace root | The working memory itself + structure map |
| tidy.md | `routines/` | Misplaced file detection routine |

---

## Summary

This memory system transforms a stateless language model into a persistent agent that:

- **Remembers** relationships and experiences (episodic memory)
- **Knows** facts and how things work (semantic knowledge)
- **Learns** from mistakes through explicit lessons
- **Evolves** through regular self-review
- **Scales** through promotion and archival

The architecture mirrors human cognition: daily experiences flow through working memory, get consolidated during "sleep" (curate/compile), and mature into long-term knowledge over time.

The result is an AI that feels like it *knows* you — because it does.

---

*Version 2.0 — March 2026*
