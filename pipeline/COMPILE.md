# COMPILE.md

Instructions for the **weekly compilation job** — a single smart pass that handles mechanical organization, intelligent extraction, graduation, and pattern recognition.

**Trigger:** Weekly cron (Sunday)  
**Model:** Opus  
**Runtime:** Isolated session, ~20-30 min

---

## Purpose

Compile is the **organizer and learner** of the memory system. It handles both the mechanical housekeeping (archive, promote, prune) and the intelligent work (extract lessons, graduate content, find patterns). These are combined into a single weekly pass because the model already has full workspace context loaded — splitting into two jobs means loading context twice for no benefit.

Opus is used because archive/prune decisions require judgment to avoid premature deletion.

---

## Phase 1: File Organization

Keep the workspace tidy.

### 1.1 Archive Old Daily Files

Move daily files older than 30 days:
```
memory/daily/2026-01-*.md → archive/daily/2026-01/
```

### 1.2 Sweep Loose Session Summaries

Move any dated `YYYY-MM-DD*.md` files from `memory/` root into `archive/daily/`.
These are session summaries created by the `session-memory` hook when `/new` is
issued. Their content is redundant with what Collect already extracts from full
session transcripts.

### 1.3 Clean Up

- Remove empty files (0 bytes)
- Remove duplicate files (same content)
- Use `trash` instead of `rm` (recoverable)

---

## Phase 2: Entity Promotion

Move overgrown content to dedicated files.

### 2.1 Check MEMORY.md

Scan for entities exceeding thresholds:
- **10+ lines** → promote to own file
- **5+ mentions** → promote to own file

### 2.2 Promotion Routing

| Entity Type | Destination |
|---|---|
| Person | `memory/contacts/<channel>-<id>.md` |
| Group | `memory/groups/<channel>-g-<name>.md` |
| Named entity or subject | `knowledge/topics/<name>.md` |

### 2.3 Promotion Steps

1. Create new file (or update existing one)
2. Move content from MEMORY.md
3. Replace in MEMORY.md with one-liner pointer
4. Update Memory Structure Map in MEMORY.md if new file created

---

## Phase 3: Staleness Pruning

Remove clearly outdated content.

### 3.1 Safe to Archive/Purge

- **30+ days old** AND marked as temporary/transient
- **Completed reminders** (past due date, marked done)
- **Superseded** — explicitly replaced by newer entry
- **Orphaned references** — link to file that no longer exists
- **Graduated content** — already moved to knowledge/ (source can be trimmed)

### 3.2 Leave Alone When Uncertain

If it's unclear whether content is still relevant, don't prune it. Flag it
for Calibrate to review during the monthly pass.

### 3.3 Safety

- Use `trash` not `rm`
- Log what was pruned in the weekly digest

---

## Phase 4: Lesson Extraction

Scan the week's daily files for implicit lessons.

### 4.1 Look For

- Mistakes and corrections ("turns out...", "actually...", "I was wrong about...")
- Friction and frustration ("this kept failing", "had to work around...")
- Successful patterns ("this worked well", "the trick is...")
- Surprises ("didn't expect...", "interesting that...")

### 4.2 Extract Format

```markdown
## Lesson Title
**Learned:** [date] (source reference)
**Context:** What happened
**Rule:** What to do / not do
```

### 4.3 Route to memory/lessons/

Add to `memory/lessons/lessons.md` (or domain-specific file if the folder has
been split: security.md, communication.md, technical.md, social.md, skills.md).

Check for duplicate lessons before adding. Update existing entries rather than
duplicating.

---

## Phase 5: Graduation Assessment

Identify content ready to move from memory/ → knowledge/.

### 5.1 Graduation Criteria

Content is ready when ALL of these are true:

| Criterion | Question |
|---|---|
| **Factual** | Is it "how X works" rather than "what we did with X"? |
| **Stable** | Has it been unchanged for 2+ weeks? |
| **Substantial** | 5+ facts or 5+ references? |
| **Transferable** | Would it help someone with no relationship context? |

### 5.2 Scan Locations

- `MEMORY.md` — technical sections, inline entities
- `memory/notes/` — staged content that may have matured
- `memory/lessons/` — lessons that are really procedures in disguise

### 5.3 Graduate Process

1. Extract factual content
2. Rewrite without temporal language ("we learned" → "X works by...")
3. Create/update file in `knowledge/topics/` or `knowledge/procedures/`
4. Update source — leave pointer or remove graduated content
5. Update MEMORY.md structure map if new file created

### 5.4 What Stays in Memory

Never graduate:
- Relationship context → stays in contacts/
- Group dynamics → stays in groups/
- Personal opinions and preferences
- Temporal context ("this month we're focused on...")
- Inside jokes and personality

---

## Phase 6: Pattern Recognition & Cross-Referencing

### 6.1 Recurring Topics

Same subject mentioned 3+ times this week? Consider:
- Creating a dedicated knowledge/topics/ file
- Noting the pattern in the weekly digest

### 6.2 Contradictions

Cross-reference for inconsistencies:
- MEMORY.md says X, but a topic file says Y
- Older daily file contradicts newer information
- Lessons that conflict with each other

Resolution: fresher wins. Update the stale source.

### 6.3 Backlinks

When an obvious connection exists between files (e.g., a person mentioned
prominently in a topic file, or a topic central to a contact's context),
add a brief reference linking them. Don't force cross-references — only add
them where the connection would genuinely help future retrieval.

---

## Phase 7: Weekly Digest

Create a summary of what happened this week.

### 7.1 Create File

```
archive/digests/YYYY-MM-DD-weekly.md
```

### 7.2 Digest Structure

```markdown
# Weekly Digest — [Date Range]

## Activity Summary
- Daily files processed: [count]
- Sessions scanned: [count]
- People active: [list]

## File Operations
- Files archived: [count]
- Entities promoted: [list]
- Content pruned: [list]

## Lessons Extracted
- [New lesson] → memory/lessons/

## Content Graduated
- [Item] moved from [source] → [destination]

## Contradictions Resolved
- [What was inconsistent] → [resolution]

## Patterns Noted
- [Emerging pattern worth watching]

## Flagged for Calibrate
- [Items needing monthly review]

---
*Compiled: [timestamp]*
```

---

## Timing

| Phase | Expected Duration |
|---|---|
| File Organization | 2-3 min |
| Entity Promotion | 2-3 min |
| Staleness Pruning | 2-3 min |
| Lesson Extraction | 3-5 min |
| Graduation Assessment | 3-5 min |
| Pattern Recognition & Backlinks | 2-3 min |
| Weekly Digest | 2-3 min |
| **Total** | **~15-25 min** |

---

*Created: 2026-03-16*
