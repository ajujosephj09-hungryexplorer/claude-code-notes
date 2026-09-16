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
