---
name: adr-writer
description: >
  Write a structured, human-readable ADR from a conversation with the user. Trigger on direct
  user requests: "write this up as an ADR", "capture this decision", "create an ADR",
  "document this", "let's record this", "should we write this up?", or any request to
  summarise or save the outcome of an architecture, design, or technology discussion.
  When in doubt, trigger — better to offer and have the user decline than to miss a real
  decision. Do NOT use when the ADR audience is agents — use adr-for-agents for that.
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

### Step 0 — Check for decision content

Before doing anything else, scan the conversation for signs of a decision: a question being
resolved, options being weighed, or a choice being made. If none is present, stop and say:

> "I don't see a decision discussed yet. Describe the decision you want to record, and I'll
> write the ADR."

Don't proceed to Step 1 until there is something to extract.

### Step 1 — Extract decision content from the conversation

Read the conversation history and identify whatever is already present:

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
| **References** | Standards, tools, articles, or documents cited |

Note gaps — elements that are missing, vague, or underdeveloped. You will address them in Step 2.

### Step 2 — Interview the user to clarify and sharpen the decision

The goal of this step is to reach a well-defined ADR foundation before writing anything. By the
end, the following should be true:

- **Problem**: clearly stated — what question is being answered, and why it matters
- **Drivers**: named and labelled — 2–4 distinct criteria that guided the decision
- **Options**: 2–4 concrete alternatives identified, including the one chosen
- **Constraints**: any hard rules or non-negotiables are surfaced

If a goal genuinely cannot be met (e.g., constraints are unknown at this stage), document it as
"not yet identified" and proceed rather than blocking.

#### Sub-step 2a — Scan for related decisions

Before asking the user anything, scan the project for existing ADRs (`ADR-*.md` in `docs/adr/`,
`decisions/`, `architecture/`, repo root). Keep any matches — you will present relevant ones to
the user during the interview as candidate related decisions.

#### Sub-step 2b — Present what you have, then fill gaps

Open by showing the user what you extracted from the conversation — even if partial. This gives
them something concrete to correct rather than a blank page.

Then work through gaps. For each gap:

1. **For open-ended gaps** (e.g., "what problem prompted this?"): ask one focused question. Do
   not ask multiple open-ended questions at once.

2. **For enumerable gaps** (drivers, options, constraints): present a candidate list drawn from
   domain knowledge and common practice for this type of decision, labelled as such. Ask the
   user which apply, which to drop, and what to add. Project-specific facts — context, history,
   existing constraints — must come from the user. Never treat a candidate as confirmed without
   a user response.

3. **For related decisions**: present the ADRs found in 2a that seem relevant. Ask the user to
   confirm which are genuinely related.

Keep going until all four goals are met or acknowledged as unknown. The user owns the decision —
if they are uncertain about something, help them reason through it, but do not resolve it for
them.

### Step 3 — Determine ADR number and save location

Use the directory discovered during the Step 2 ADR scan. Infer the next sequence number from
existing files.

Confirm with one question: "I'll save this as ADR-0005 in `docs/adr/` — correct?"

If no existing ADRs were found, ask the user for both the number and preferred location.

Status defaults to `Proposed`. Only change if the user specifies otherwise.

### Step 4 — Fill in the template

Load `ADR_TEMPLATE.md` from this skill directory. Populate every section, replacing each
`{placeholder}` with content derived from the conversation. Follow these rules:

- **Management summary**: Write 2–4 non-technical sentences. Explain *what was decided* and *why
  it matters*, without acronyms or implementation detail. This section is for non-technical
  stakeholders, not engineers.
- **Context**: Summarise the situation and requirements as bullet points, then close with the
  central question as a plain sentence.
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

### Step 5 — Review with the user

Ask the user: "Would you like to review this section-by-section (default) or see the whole
document at once?"

- **Section-by-section** (default): Present each section in template order, wait for feedback,
  apply changes, then move to the next. Once a section is approved, don't revisit it unless
  the user asks.
- **Whole document**: Present the full draft, collect feedback in one round, apply all changes,
  confirm the result.

### Step 6 — Output the ADR

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
confirmed in Step 3.

Filename: `ADR-{number}-{kebab-case-title}.md` (e.g. `ADR-0001-bi-temporal-fact-storage.md`)

