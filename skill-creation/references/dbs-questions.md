# The DBS questions, by tier

Ask one at a time. Wait for the answer before asking the next. Do not front-load all of them at once — unless the install record says the user chose batch or infer-and-confirm mode, in which case follow that.

**Skip any question already answered by the trigger message or by brainstorming.** Re-asking what the user already told you is the fastest way to lose them.

---

## Base questions — all tiers (Q1–Q6)

**Q1a — Task**
"What specific task should this skill automate? One sentence."

**Q1b — Trigger phrases**
"What would you actually say to kick this off on a normal workday? Give me two or three — they seed the trigger list."
(These feed the description, which gets written richer at build time per the guide in SKILL.md Step 6b — not just these phrases verbatim.)

**Q2 — Input and output**
"What does the input look like? And the output — format, where it lands, roughly how long?"

**Q3 — Blueprints check**
"Does the output have to match a specific voice, follow a specific structure, or use knowledge the AI doesn't have on its own — pricing, customer profiles, your writing style, a process you've refined?"

→ **Yes:** plan a `references/` folder. Follow up: "Which reference files would it need — a voice guide, a structure doc, worked examples, pricing, customer profiles?"
→ **No:** no references folder unless Q4 pulls one in.

**Q4 — Solutions check**
"Does it need to call an external API, generate a formatted file like a .docx or .pdf, or produce output that must come out identical every time — calculations, templated documents?"

→ **Yes:** plan a `scripts/` folder. Follow up: "Which scripts — describe the operation and what it produces."
→ **No:** Direction only, or Direction + Blueprints.

**Q5 — Behavior**
"Should it ask clarifying questions mid-run, or execute silently and hand you the finished output?"

**Q6 — Install location**
"Where should the skill live? The default is the skills folder we set up at install — does that work, or does this one belong somewhere else (a specific project, a shared repo)?"

---

## Tier 2 adds

**Q7 — Edge cases**
"What should it do when the input is missing, incomplete or ambiguous — stop and ask, make a best guess and flag it, or skip that piece and carry on?"

---

## Tier 3 adds

All of Tier 2's questions, plus:

**Q8 — Integrations**
"Which external APIs or services does it call? What's the auth — an API key, OAuth, a connected tool? And what does a response look like?"

**Q9 — Mechanical steps**
"Are there purely mechanical steps in this — file reads, reformatting, lookups — that need no judgment at all? List them if so."
(Use the answer yourself to decide whether cheap-model subagent dispatch applies. Do not ask the user to evaluate that; they don't have to know how your platform's models are priced.)

**Q10 — Success and failure**
"What does a successful run look like, end to end? And what's the clearest failure — how would you know it broke?"
