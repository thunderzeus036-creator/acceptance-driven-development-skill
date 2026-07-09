# Acceptance-Driven Development — Install Kit

## What This Is

A Claude Code skill pack that implements **acceptance-criteria-driven development**:

1. **Gate 1** — Agent brainstorms with you → design document → you review
2. **Gate 2** — Agent drafts acceptance criteria from design → you approve
3. **Phase 0~6** — Agent executes in batch → self-reviews → verifies → loops until every `[ ]` is `[x]` or `[!]`

---

## File Structure

```
english-release/
├── README.md                              # This file
├── skills/                                # ← Copy to ~/.claude/skills/
│   ├── acceptance-driven-development/
│   │   ├── SKILL.md                       # Main skill (~405 lines)
│   │   ├── IMPROVEMENT-GUIDE.md           # Design principles for future maintainers
│   │   └── references/
│   │       └── framework-review-checklist.md  # Qt/React/Django/Go review checklist
│   └── project-experience/
│       └── SKILL.md                       # Sub-skill: project experience extraction
├── projects/                              # ← Copy to your project docs folder
│   ├── templates/
│   │   ├── ac-template.md                 # AC document template
│   │   └── project-index.md               # Project index (Dataview)
│   └── AlarmClock/                        # Example project
│       └── AC.md
└── codex-port/
    └── CODEX-ADAPTATION.md                # Codex CLI adaptation guide
```

---

## Installation

### 1. Install Superpowers (if not already)

Required sub-skills: `brainstorming` `writing-plans` `subagent-driven-development` `test-driven-development` `verification-before-completion` `finishing-a-development-branch`

If missing, the skill degrades gracefully — it tells you what's unavailable.

### 2. Copy Skill Files

```
skills/acceptance-driven-development/  →  ~/.claude/skills/acceptance-driven-development/
skills/project-experience/             →  ~/.claude/skills/project-experience/
```

Windows: `C:\Users\<you>\.claude\skills\`

### 3. Copy Templates

Create a `projects/` folder in any directory (Obsidian vault or plain folder). Copy the template files there.

### 4. Restart Claude Code

New skills load on the next session.

### 5. Verify

Type `/skills` — you should see `acceptance-driven-development` and `project-experience`.

---

## AC Statuses

| Mark | Meaning | Who decides |
|------|---------|-------------|
| `[ ]` | Not implemented | — |
| `[~]` | Partially done | Agent or user |
| `[x]` | Verified | Agent runs verification |
| `[!]` | Implemented, needs user check | Agent marks, user confirms |
| `[-]` | Deprecated | Agent or user |
| `[>]` | Deferred / backlog | Agent or user |

---

## Usage

### New Project

```
"I want to build a photo browser. Use ADD skill."
```

Agent: Gate 1 brainstorming → design doc → you review → Gate 2 AC → you approve → batch execution.

### Existing Project

```
"Implement AC-41 through AC-54 for my project"
```

Agent batch-executes, no interruption.

### Mid-Development Change

```
"Tags can't be deleted — add a delete button"
```

Agent updates AC first (Phase 3.5), asks for your confirmation, then implements.

---

## Example Project

See `projects/AlarmClock/AC.md` for a complete acceptance criteria document.

---

## Dependencies

| Name | Type | Required? |
|------|------|------|
| Superpowers skill pack | Public | Recommended (degrades gracefully) |
| project-experience | Included here | ✅ Yes |
| Obsidian Vault | Software | No — any folder works |

---

## FAQ

**Q: Agent can't find my project docs?**
A: The skill searches `~` for `.obsidian/` or `AC.md` files, checks the current directory, then asks you.

**Q: Can I use this without Superpowers?**
A: Yes. The skill tells you what's missing and degrades — you lose brainstorming, TDD, and code review but the core AC loop still works.

**Q: Agent says "done" but ACs still show `[ ]`?**
A: Phase 6 hard gate prohibits this. If it happens, confirm the skill file was copied correctly and Claude Code was restarted.

**Q: What's the difference between `[!]` and `[~]`?**
A: `[!]` = Agent implemented it but can't verify (needs you to test). `[~]` = Agent implemented it partially (known edge issues remain).

**Q: Mode A vs Mode B?**
A: Mode A = batch all `[ ]` items with subagent review. Mode B = lightweight single change (1-2 ACs) with self-review. Both go through Phase 3.5 first.
