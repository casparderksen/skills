---
name: refactor-claude-md
description: >
  Refactor CLAUDE.md files to be lean, session-focused, and discoverable. Use this skill
  whenever the user asks to clean up, slim down, refactor, or audit a CLAUDE.md file, or
  when a CLAUDE.md exceeds ~100 lines and contains design decisions, reference docs, or
  content that doesn't need to be in every session. Also trigger when user says "my
  CLAUDE.md is too long", "move stuff out of CLAUDE.md", or "set up progressive disclosure".
---

# Refactor CLAUDE.md

CLAUDE.md loads into every session. Every line costs tokens. Only include what Claude
needs every single time — commands, non-obvious constraints, and a pointer index.
Everything else goes elsewhere and is loaded on demand.

---

## Target

- **50 lines** ideal. 200 lines hard ceiling.
- No design decisions, no reference docs, no content derivable from the codebase.

---

## What belongs in CLAUDE.md

| Section | Content |
|---|---|
| **WHAT & WHY** | One paragraph: tech stack and why the architecture exists |
| **HOW** | Exact commands: run tests, lint, build, start dev server |
| **Progressive Disclosure** | Short index — file paths and one-line descriptions |

**Keep**: non-obvious constraints, warnings, commands, pointers.  
**Remove**: anything derivable by reading directory listings or source code without project history or context.

---

## Workflow

### Step 1 — Read and classify

Read the existing CLAUDE.md. For each section or chunk, classify:

| Category | Destination |
|---|---|
| Commands and HOW-TO | Keep in CLAUDE.md |
| Non-obvious constraints or gotchas | Keep in CLAUDE.md |
| Architecture overview (brief) | Keep in CLAUDE.md |
| Design decisions with rationale | → ADR (`docs/adr/`) — see criteria below |
| Reference docs, conventions, patterns | → `docs/agent/<topic>.md` |
| Derivable from code or directory structure | Delete |
| Session notes, recorded decisions | Delete |

Show the user a classification table before moving anything.

### Step 2 — Write reference docs

For each chunk classified as reference: create `docs/agent/<topic>.md`.  
One file per topic. Start with a one-line summary. Keep them focused.

### Step 3 — Write ADRs

For each chunk classified as a design decision, evaluate against all three criteria:

1. **Hard to reverse** — the cost of changing your mind later is meaningful
2. **Surprising without context** — a future reader will wonder "why did they do it this way?"
3. **The result of a real trade-off** — genuine alternatives existed and one was chosen for specific reasons

If any criterion is missing, skip the ADR. When all three hold, write an ADR using `ADR-TEMPLATE.md` in this skill directory.

ADRs describe the **current design** — not history, change logs, or how things evolved. Write as if explaining the present state to someone who wasn't there.

Scan `docs/adr/` for the highest existing number; increment by one.  
Create `docs/adr/` lazily — only when the first ADR is needed.

### Step 4 — Rewrite CLAUDE.md

Write a new CLAUDE.md with three sections only:

```markdown
# Project Name

One-paragraph WHAT & WHY: tech stack, key architectural choices, purpose.

## Commands

| Command | Purpose |
|---|---|
| `<cmd>` | <what it does> |

## Docs

- [`docs/agent/topic.md`](docs/agent/topic.md) — one-line description
- [`docs/adr/`](docs/adr/) — architectural decision records
```

Omit any section that has no content. Never invent content — only use what was in the original.

### Step 5 — Confirm and write

Show the user:
1. What's being deleted
2. What's moving and where
3. The new CLAUDE.md draft

Get confirmation before writing any files.

---

## Multi-module projects

If the repo has multiple modules:
- Root `CLAUDE.md`: repo-wide commands and architecture overview, pointer to module dirs
- Per-module `CLAUDE.md`: module-specific commands and constraints only
- Shared docs: `docs/` at root; module-specific docs: `<module>/docs/`

---

## Progressive Disclosure index format

Each entry in the index should be a single line:

```
- [`docs/agent/testing.md`](docs/agent/testing.md) — test conventions and fixture setup
```

Point to files, not directories, unless the directory itself is the entry point (e.g., `docs/adr/`).
