---
name: project-experience
description: Use when writing code, designing architecture, choosing dependencies, or implementing features — especially when the task involves technology or domains (C++, Qt, astronomy, desktop apps, image processing, scientific computing) where the author has prior project experience documented in the vault. Also use when the user mentions starting a new project or asks you to follow their coding style.
---

# Project Experience

## Overview

Read the project documentation library — a collection of structured project docs (tech stack, architecture, reusable patterns, known pitfalls, coding conventions) stored in any folder (Obsidian vault or plain directory). Before writing any code, mine these documents for constraints and patterns that generic AI assistants would miss.

**Core principle:** Past projects encode years of hard-won engineering judgment. A 3-minute read of their docs saves hours of repeating their mistakes.

## When to Use

```
New task received ──► Is it code/architecture/dependency related?
                              │
                    ┌─────────┴──────────┐
                    │ YES                │ NO → skip
                    ▼
             Run Phase 0: locate vault
                    │
          ┌─────────┴──────────┐
          │ Found               │ Not found → skip, tell user
          ▼
     Phase 1–5: extract and apply experience
```

**Specific triggers:**
- User asks you to write, design, or implement code
- User mentions "new project", "refactor", "choose library"
- Task involves: C++, Qt, FITS, astronomy, desktop GUI, image processing, scientific computing
- User says "follow my style" or "similar to my other projects"
- You're about to make an architectural decision
- You're about to pick a dependency or library

**Skip when:**
- Task is purely conversational (answering questions, explaining concepts)
- Task is about editing markdown files, notes, or documentation
- Code task is trivial (single-line fix, typo, formatting)

## Phase 0: Locate the Project Docs — REQUIRED before all other phases

The project documents live in any folder (Obsidian vault or plain directory). You **cannot** use relative paths because your working directory may be anywhere. You MUST locate the docs root first.

### Step 0.1: Locate the project docs root (try in order)

1. **Search home directory:** `Glob pattern="**/.obsidian"` or `Glob pattern="**/AC.md"` from `~`
2. **Check current directory as fast path:** `Read ".obsidian/app.json"` or search for `AC.md` in subfolders
3. **Ask user:** "Where are your project docs? (can be an Obsidian vault or any folder)"
4. If user has no folder yet: "I can create one. Which directory?" → create `<chosen>/projects/` → VAULT.

### Step 0.2: Find project documents

Project docs live in any subfolder under VAULT (e.g. `projects/`, `claude/`, `my-projects/`). The folder name is not fixed — find it by content:

1. Try `Read "<VAULT>/projects/project-index.md"` — if exists, store `DOCS=projects`
2. If not found, search: `Glob pattern="**/project-index.md" path="<VAULT>"`
3. If still not found, search for any project doc: `Glob pattern="**/AC.md" path="<VAULT>"` — the parent folder is your docs root
4. If nothing found, tell the user and offer to create: "No project docs found yet. Create a `projects/` folder with templates?" → on approval, create the folder and copy/add templates.

Store `DOCS` (the folder name under VAULT where project docs live) for all subsequent paths. All file paths use `<VAULT>/<DOCS>/...` where `<DOCS>` is the discovered folder name.

## Phase 1: Survey

Read the project index (`<VAULT>/<DOCS>/project-index.md`) to understand what exists.

If only Dataview code blocks are present (the index is auto-generated), also run a direct file listing:

```
Glob pattern="<DOCS>/*/**.md" path="<VAULT>"
```

From the survey, extract:
- How many projects exist
- Their tags (languages, frameworks, domains) — read frontmatter of each project document found
- Their status (maintained, completed, archived)

This takes under 15 seconds.

## Phase 2: Match — Find Relevant Projects

Read the frontmatter tags of each project document and compare against the current task:

| Match dimension | How to check | Example |
|-----------------|-------------|---------|
| **Technology stack** | Same language, framework, build system | Qt + C++ task → match EgainMeasure, FitsCompare |
| **Domain** | Same industry, data format, problem type | FITS astronomy task → match both projects |
| **Architecture style** | Same pattern (MVC, pipeline, event-driven) | Desktop GUI → match Qt projects |
| **Pattern reuse** | Task needs a specific pattern | Async processing → check Generation Counter in both projects |

**Match threshold:** One dimension match = read that project. Two or more = mandatory deep read.

If zero matches across all dimensions, state this explicitly and proceed with generic best practices.

## Phase 3: Extract — Targeted Reading

**Do NOT read full project documents.** Read only the sections relevant to the current task:

| Current task needs... | Read these sections |
|----------------------|---------------------|
| Choose tech stack / dependencies | Tech Stack, Key Dependencies |
| Design architecture | Architecture, Data Flow, Core Modules |
| Write async/multi-threaded code | Reusable Patterns, Edge Cases & Defensive Design |
| Handle file I/O / external libraries | Edge Cases & Defensive Design, Key Dependencies |
| Avoid known pitfalls | Technical Debt & Risks (especially ✅ resolved items) |
| Write tests | Testing |
| Set up build config | Configuration, Quick Start |
| Match author's coding style | Architecture, Core Modules, Overall Assessment |
| Export/report generation | Key Business Flows |
| Cross-project consistency | Related Projects |

**Constraint:** Read at most 2 project documents fully. For additional projects, extract only the Reusable Patterns and Technical Debt sections.

## Phase 4: Synthesize — Write the Experience Briefing

Compose a briefing and output it to the conversation. It serves as a persistent reference for the rest of the session:

```markdown
## Project Experience Briefing

### Matched Projects
- [[Project A]] — match: Qt + C++ + domain X
- [[Project B]] — match: Qt + C++ + domain Y

### Coding Preferences
1. [preference 1] — evidence: Project A Architecture, Project B Core Modules
2. [preference 2] — evidence: [source]

### Pitfalls to Avoid
| Pitfall | Status | Source | Strategy |
|---------|--------|--------|----------|
| ... | ✅/⚠️ | ... | ... |

### Reusable Patterns
1. **[pattern name]** — [one line] — applies to: [X]
2. ...

### Quality Baseline
- Code organization: [inferred]
- Error handling: [inferred]
- Test coverage: [inferred]
```

## Phase 5: Apply

The briefing is your constraint system. For every subsequent decision:

1. **Choose a library** → check Pitfalls to Avoid
2. **Design a module** → check Reusable Patterns
3. **Write boilerplate** → check Coding Preferences
4. **In doubt** → re-read the relevant project section

**Reference the briefing explicitly in your responses:**

> "I see you used Generation Counter in Project A and Project B for async race conditions — I'll use the same approach here."

## Quick Reference

| Phase | Action | Time |
|-------|--------|------|
| 0. Locate | Current dir → search → ask. Discover docs folder by content | 10s |
| 1. Survey | Read index + glob project files | 10s |
| 2. Match | Compare tags vs task | 5s |
| 3. Extract | Read targeted sections from 1-2 projects | 30-60s |
| 4. Synthesize | Write briefing to context | 30s |
| 5. Apply | Reference briefing in all subsequent decisions | Ongoing |

**Total overhead: ~2 minutes.**

## Common Mistakes

| Mistake | Why it happens | Fix |
|---------|---------------|-----|
| Using relative paths for vault files | Working directory may be outside the vault | **ALWAYS use absolute paths** starting with the vault root discovered in Phase 0 |
| Skipping Phase 0 | "I already know where the docs are" | Never guess. Verify with a read, then fallback to search or ask. |
| Not writing the briefing | "I'll remember what I read" | Without a written briefing, lessons fade within 10 message turns. Write it down. |
| Ignoring ✅ resolved items | "That's already fixed, not relevant" | Resolved items show exactly what patterns to use INSTEAD. |
| Reading entire project docs | "More context is better" | Trust the section mapping table. Targeted reading is faster and cleaner. |
| Not citing sources | "The author knows their own projects" | Citations show your reasoning is evidence-based, not generic advice. |
