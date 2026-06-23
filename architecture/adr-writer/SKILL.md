---
name: adr-writer
description: >
  Writes a structured ADR from conversation history using a standard template. Trigger when user
  says "write this up as an ADR", "capture this decision", "create an ADR", or similar. Also
  trigger when user asks for a summary after discussing architecture, design options, or trade-offs.
---

# ADR Writer

Extracts and structures the outcome of a conversation into a well-formed Architectural Decision
Record, following the project's standard ADR template.

---

## When to Use

Trigger this skill when:
- The user asks to write, capture, or export an ADR
- A conversation has explored a technical or architectural decision with options, drivers, and a
  chosen outcome, and the user wants it documented
- The user says anything like: "write this up", "document this decision", "create an ADR",
  "save this as an ADR", "turn this into an ADR"

---

## Workflow

### Step 1 — Extract decision content from the conversation

Read the conversation history and identify:

| Element | Where to look |
|---|---|
| **Decision topic** | What technology, architecture, or design question was being resolved |
| **Context & problem** | What requirements, constraints, or circumstances made the decision necessary |
| **Decision drivers** | Explicit or implicit criteria that guided evaluation (cost, team skill, standards, performance, etc.) |
| **Options considered** | All alternatives discussed, even briefly or informally |
| **Chosen option** | The option selected, and the reasoning given |
| **Positive consequences** | Benefits, improvements, or capabilities unlocked by the decision |
| **Negative consequences** | Trade-offs, costs, risks, or obligations introduced |
| **Constraints** | Any hard rules the implementation must respect |
| **Related decisions** | Other ADRs or decisions mentioned or implied |
| **References** | Standards, tools, articles, or documents cited |

If any critical element is missing or ambiguous, ask the user one focused question before
proceeding. Do not ask multiple questions at once.

### Step 2 — Determine ADR number and save location

Scan the project for existing ADRs by searching for files matching `ADR-*.md` in common
locations (`docs/adr/`, `decisions/`, `architecture/`, repo root). Infer the next sequence
number and use the discovered directory as the save location.

Confirm with one question: "I'll save this as ADR-0005 in `docs/adr/` — correct?"

If no existing ADRs are found, ask the user for both the number and preferred location.

Status defaults to `Proposed`. Only change if the user specifies otherwise.

### Step 3 — Fill in the template

Populate every section of the template below. Follow these rules:

- **Management summary**: Write 2–4 non-technical sentences. Explain *what was decided* and *why
  it matters*, without acronyms or implementation detail. This section is for non-technical
  stakeholders, not engineers.
- **Context**: Summarise the situation and requirements as bullet points, then close with the
  central question in bold.
- **Decision Drivers**: Extract named, labelled drivers. Each driver must have a name and a
  one-sentence explanation of why it matters. Derive drivers from the conversation even if the
  user did not label them explicitly.
- **Considered Options**: List all options discussed. Mark the chosen one with *(chosen)*.
- **Decision Outcome**: State the chosen option, then explain in 1–3 paragraphs why it was
  selected and how it satisfies the drivers.
- **Positive / Negative Consequences**: Use the exact bullet format from the template. Be
  concrete and specific — avoid generic statements like "improves quality".
- **Pros and Cons per Option**: Include every option. For the chosen option, mark the heading
  with *(Chosen)*. Each option needs at least one "Good, because" and one "Bad, because" bullet.
- **Architecture Summary**: If a diagram or structure was described in the conversation, render
  it as ASCII art inside a code block. Otherwise write a brief prose summary and omit the code
  block.
- **Key Constraints**: Number each constraint. Use a bold label. State what is required and what
  is prohibited. Only include constraints that were explicitly established in the conversation.
- **Related Decisions**: Link to other ADRs mentioned. Use `ADR-XXXX` if numbers are unknown.
- **References**: List standards, tools, or documents mentioned. Use markdown link syntax where
  a URL is known.

### Step 4 — Review with the user

Ask the user: "Would you like to review this section-by-section (default) or see the whole
document at once?"

- **Section-by-section** (default): Present each section in template order, wait for feedback,
  apply changes, then move to the next. Once a section is approved, don't revisit it unless
  the user asks.
- **Whole document**: Present the full draft, collect feedback in one round, apply all changes,
  confirm the result.

### Step 5 — Output the ADR

Before producing final output, verify:

- [ ] Every template section is populated — no raw placeholders remain
- [ ] Management summary contains no technical jargon or acronyms
- [ ] All options from the conversation appear in "Considered Options" and "Pros and Cons"
- [ ] Chosen option is marked with *(chosen)* in the options list
- [ ] Each decision driver has a distinct, named label
- [ ] Positive and negative consequences are specific, not generic
- [ ] Constraints are numbered and bold-labelled
- [ ] Filename follows the `ADR-{number}-{kebab-case-title}.md` convention

Produce the complete ADR as a markdown code block in the chat, then save it to the location
confirmed in Step 2.

Filename: `ADR-{number}-{kebab-case-title}.md` (e.g. `ADR-0001-bi-temporal-fact-storage.md`)

---

## Template

See `ADR_TEMPLATE.md` in this skill directory for the full template. Load it
and populate every section, replacing each `{placeholder}` with content derived
from the conversation.
