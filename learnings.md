# Claude Code Learnings

Accumulated tips, patterns, and "aha" moments from working with Claude Code across projects.

---

## 1. Auto-Memory System (2026-09-15)

**What:** Claude Code has a persistent, file-based memory system that remembers context across conversations.

**How it works:**
- Memory files are stored at `~/.claude/projects/<path-encoded-directory>/memory/`
- Each project/repo gets its own separate memory folder
- `MEMORY.md` is an index file loaded at the start of every conversation — it's a table of contents, not a memory itself
- Individual memory files are markdown with frontmatter (name, description, type)

**4 memory types:**

| Type | Prefix | Purpose | Example |
|------|--------|---------|---------|
| `user` | `user_` | Who you are — role, expertise, preferences | "GM/Product exec, Duke MBA, Toronto" |
| `feedback` | `feedback_` | How you want Claude to work — corrections + confirmed approaches | "Always add Category column to tables" |
| `project` | `project_` | Current state of work — decisions, status, deadlines | "Banyan Round 2 done, Round 3 pending" |
| `reference` | `reference_` | Pointers to external systems | "Bugs tracked in Linear project INGEST" |

**Key details:**
- Memory is project-scoped — only loaded when you open Claude Code in that project folder
- Files are plain markdown — you can read, edit, or delete them directly in Finder
- Claude saves memories automatically when it learns something useful, or when you explicitly say "remember this"
- To see your memories: run `/memory` in Claude Code, or browse `~/.claude/projects/` in Finder

**When it's useful:** Understanding why Claude "remembers" things between sessions, cleaning up stale memories, or debugging when it acts on outdated info.

---

## 2. The /memory Menu vs Auto-Memory (2026-09-15)

**What:** `/memory` shows 4 options that are actually two separate systems. Easy to confuse.

**The /memory menu:**

| # | Option | File | Scope |
|---|--------|------|-------|
| 1 | Parent CLAUDE.md(s) | Any `CLAUDE.md` in parent directories | All repos under that parent folder |
| 2 | Project memory | `./CLAUDE.md` in current repo | This repo only |
| 3 | User memory | `~/.claude/CLAUDE.md` | Every project on your machine |
| 4 | Open auto-memory folder | `~/.claude/projects/.../memory/` | This repo only |

**Key distinction:**
- Options 1-3 are **CLAUDE.md files** — manually edited, contain instructions/rules
- Option 4 is **auto-memory** — files Claude creates automatically (user_, feedback_, project_ prefixes)

**CLAUDE.md hierarchy:** Claude Code walks UP the directory tree and loads every CLAUDE.md it finds. It does NOT look sideways into sibling folders. So a parent-level CLAUDE.md is shared by all child repos, but sibling repos don't see each other's CLAUDE.md.

**User memory vs Parent CLAUDE.md:**
- User memory (`~/.claude/CLAUDE.md`) — global, applies everywhere on your machine
- Parent CLAUDE.md — applies only to repos under that specific parent folder
- User memory does NOT auto-populate. You have to write to it manually.

**When it's useful:** Knowing where to put instructions depending on how broadly they should apply. Global preferences (no AI slop, OS constraints) go in User memory. Shared project instructions go in a parent CLAUDE.md.

---

## 3. Full Loading Sequence — What Claude Knows Before You Type (2026-09-15)

**What:** The complete order of everything Claude Code loads when you open a new terminal in a repo.

**Step 1: System prompt (built into Claude Code)**
- Tool usage rules, tone, safety, how to commit/create PRs
- You never see this, can't edit it

**Step 2: User memory (global)**
- `~/.claude/CLAUDE.md`
- Applies to every project on your machine
- Example: "No AI slop, macOS Big Sur, GitHub username"

**Step 3: Parent CLAUDE.md (walks up the directory tree)**
- Every `CLAUDE.md` found in parent folders above the current repo
- Example: `~/Desktop/AI Learning/CLAUDE.md` — shared pointers for all repos in that folder

**Step 4: Project CLAUDE.md**
- `./CLAUDE.md` in the current repo
- The main instruction set for this specific project

**Step 5: Auto-memory index**
- `~/.claude/projects/<path-encoded-dir>/memory/MEMORY.md`
- One-line summaries of all saved memories (user, feedback, project, reference)

**Step 6: Git status snapshot**
- Current branch, staged/unstaged changes, recent commits

**Step 7: Metadata**
- Today's date, OS version, model name, available skills/agents/tools

**Step 8: Instruction-driven context (on first interaction)**
- Files that the Project CLAUDE.md tells Claude to read at session start
- These are project-specific — whatever the CLAUDE.md says to load

**Steps 1-7 are in Claude's head before you say a word. Step 8 happens on the first interaction.**

**Beyond Step 8 — on-demand loading:**
Everything else is pulled in only when the conversation needs it. Examples:
- "Create a tailored resume" → Claude loads the skill file (`.claude/skills/resume-tailor.md`), reads the JD, and pulls relevant experience from the context library
- "Prep me for an interview at Stripe" → Claude checks `insider-data/company-intel/stripe.md` for interview intel
- "Write me a LinkedIn comment" → Claude reads the framework from a sibling repo because the Parent CLAUDE.md points to it
- "What's my Banyan status?" → Claude reads the individual auto-memory file (`project_banyan_status.md`) referenced in the MEMORY.md index

The pattern: Steps 1-8 are automatic context. Everything after is Claude reaching for the right file based on what you ask.
