# Tier routing

## How to classify a skill

Tier is a **derived scope label**, not a decision to put to the user. Read the description, match the signals below, set the tier **silently**. It sizes the work: the question set, brainstorm-or-not, companion-project-folder-or-not. Surface it once in the Step 5 spec block; never spend a round-trip confirming it.

The gating questions re-derive what the tier implies anyway — Q3 (needs references?) sets Blueprints, Q4 (needs scripts or an API?) sets Solutions, and the novelty check gates brainstorming. So classify to route the work, then let the questions confirm the substance.

**Override:** anything that has to match a person's or a company's voice or tone is Tier 2 minimum, whatever its line count. Voice is Blueprint knowledge by definition.

---

## Tier 1 — Simple

**DBS layers:** Direction only (or Direction + one lightweight Blueprint)
**Length target:** under 200 lines
**Process:** base questions (Q1a–Q6) → spec block → build → test → ship
**No** brainstorming. **No** companion project folder.

**Signals:**
- Mechanical input/output: parse, extract, format, convert, read X return Y
- No voice or tone matching
- No external API calls
- Output is inline text or structured text (JSON, a markdown table) returned in the conversation
- Examples: extract action items from a transcript · reformat a CSV as JSON · pull one field out of a contact record

---

## Tier 2 — Medium

**DBS layers:** Direction + Blueprints
**Length target:** 200–400 lines
**Process:** base questions + Q7 (+ Q7b if it writes anything that persists) → spec block → build → test → ship
**Creates** a companion project folder (README + STATUS + DESIGN) *if the install record says complex skills get one*.

**Signals:**
- Output must match a specific voice or tone
- The task needs business-specific knowledge or structure the AI doesn't have on its own
- The deliverable follows a house format that's been refined over time
- **Output may be written to a file** — a note, a draft, a markdown document the AI composes directly. Writing a file the AI *writes with words* is Tier 2; it's only Tier 3 when code has to generate it
- Examples: an outreach drafter in the founder's voice · a meeting-notes writer in the team's template · a proposal reviewer against the company's standards

---

## Tier 3 — Complex

**DBS layers:** Direction + Blueprints + Solutions
**Length target:** 400–500 lines max — past 500, reference material belongs in `references/`
**Process:** brainstorm if novel → base questions + Q7 + Q7b + Q8–Q10 → spec block → build → test → ship
**Creates** the full companion project folder (README, STATUS, DESIGN, PLAN, sessions/, artifacts/) *if the install record says so*.

**Signals:**
- Calls an external API or service
- Produces **generated or byte-identical** files — .docx, .pdf, images, spreadsheets, database rows: anything a script must build rather than the AI compose
- Output must be identical every run (calculations, generated documents)
- A multi-step pipeline with subagents or scripts
- Examples: an enrichment pipeline over a CRM API · a proposal generator with PDF output · an invoice builder

---

## Brainstorming

Delegate to a structured brainstorming skill when **all three** are true:

1. Tier is 2 or 3
2. The user gave no clear analogy to an existing skill ("like X but for Y")
3. The architecture or interaction is genuinely open. **The test:** can you write the spec block's Direction, Blueprints and Behavior lines right now without inventing anything? If yes, it isn't open — skip brainstorming. If you'd be guessing at any of the three, it is.

Skip brainstorming when: Tier 1 (always) · the user gave a clear analogy · the description is specific enough that the questions will resolve everything.

**When delegating**, say: "This feels novel enough to brainstorm the shape before we spec it. Want to run that first, or do you already have a clear mental model?"

**If no brainstorming skill is installed** — the common case — don't skip it and don't invent a long process. Run these three questions inline, in order, one at a time, and write the answers into the spec:

1. "Walk me through the last time you did this by hand, start to finish. What did you look at first, and what did you produce?"
2. "What's the part you'd be annoyed to see the AI get wrong — the judgment call in the middle?"
3. "Is there an existing skill or workflow this should behave like, or is it its own thing?"

That's the whole fallback. Its job is to surface the real sequence and the real judgment call before the structured questions start, so the questions fill gaps instead of discovering the task.
