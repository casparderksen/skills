---
name: adr-for-agents
description: >
  Write ADRs intended to be read by AI agents — minimal, outcome-focused, no extended
  rationale. Use when the audience is an agent that needs to load the decision as context,
  not a human reading for understanding. Trigger when the user says "write an ADR for the
  agents", "add this to docs/adr for context loading", or when another skill (e.g.
  refactor-claude-md) needs to persist a decision. Do NOT use when the user wants a
  human-readable ADR — use adr-writer for that.
---

# ADR Writer (for agents)

ADRs written by agents are consumed by agents. Keep them short: what was decided and why,
in one paragraph. Extended rationale pollutes context — if the reason fits in a sentence,
don't write a section.

---

## When to write an ADR

All three must be true:

1. **Hard to reverse** — changing course later carries meaningful cost
2. **Surprising without context** — a future reader would wonder "why on earth did they do it this way?"
3. **Real trade-off** — genuine alternatives existed and one was chosen for specific reasons

If any criterion is missing, skip it. Easy reversals don't need records. Obvious choices don't need justification. The inevitable path doesn't need an ADR.

### What qualifies

- **Architectural shape.** "We're using a monorepo." "The write model is event-sourced, the read model is projected into Postgres."
- **Integration patterns between contexts.** "Ordering and Billing communicate via domain events, not synchronous HTTP."
- **Technology choices that carry lock-in.** Database, message bus, auth provider, deployment target. Not every library — just the ones that would take a quarter to swap out.
- **Boundary and scope decisions.** "Customer data is owned by the Customer context; other contexts reference it by ID only."
- **Deliberate deviations from the obvious path.** "We're using manual SQL instead of an ORM because X." Anything where a reasonable reader would assume the opposite.
- **Constraints not visible in the code.** "We can't use AWS because of compliance requirements." "Response times must be under 200ms because of the partner API contract."
- **Rejected alternatives when the rejection is non-obvious.** If you considered GraphQL and picked REST for subtle reasons, record it — otherwise someone will suggest GraphQL again in six months.

---

## Output style

ADR content must be terse and direct. Drop filler words and unnecessary preamble. If something can be said in fewer words, use fewer.

---

## Format

ADRs live in `docs/adr/` with sequential numbering: `0001-slug.md`, `0002-slug.md`, etc.

Scan `docs/adr/` for the highest existing number and increment. Create the directory lazily — only when writing the first ADR.

### Template

```markdown
# {Short title of the decision}

{1–3 sentences: context, decision, and why.}
```

That's it. An ADR can be a single paragraph. The value is in recording *that* a decision was made and *why*.

### Optional sections

Only include these when they add genuine value. Most ADRs won't need them.

- **Status** frontmatter `(proposed | accepted | deprecated | superseded by ADR-NNNN)` — useful when decisions are revisited
- **Considered Options** — only when rejected alternatives are worth remembering
- **Consequences** — only when non-obvious downstream effects need calling out
