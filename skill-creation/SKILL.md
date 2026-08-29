---
name: skill-creation
description: Use when the user wants to build, create, or scaffold a new skill, automate a repeating task, or turn a workflow or prompt into a reusable skill. Triggers — "new skill", "build a skill", "create a skill", "make me a skill", "I need a skill that", "skill for X", "automate this", "turn this into a skill", "we keep doing this by hand". Also use when reviewing or editing an existing skill. Use this instead of scaffolding a skill folder ad hoc.
license: MIT
metadata:
  version: "1.0"
---

A single entry point for building any new skill, end to end: gate the idea, size it, interview for what it needs, get the spec confirmed, write the files, install it where your platform finds it, and test it twice. This skill does the writing itself — it never hands off to an external skill-builder.

**DBS — the structural model this funnel applies:**
- **Direction** = `SKILL.md` — the workflow. Always required. Tells the AI what to DO.
- **Blueprints** = `references/` — domain knowledge the AI lacks (voice, structure, pricing, customer profiles, frameworks, formats). Loaded on demand. Tells the AI what to KNOW.
- **Solutions** = `scripts/` — code for output that must be identical every run (file generation, API calls, calculations).

Every question below exists to decide which of those three layers the skill needs. A skill with a `references/` folder it doesn't need is bloat; a skill missing one hallucinates the thing it should have known.

> **Install-time settings.** Read **every row** of the Install record in the skills index (`<skills>/README.md`) before Step 4 — not just the six decision-point rows. It tells you where skills live, whether folders take an ordering prefix, whether complex skills get a companion project folder and where, how the dry-run gate runs, how skills get invoked, and which interview mode to use. **Two rows are not decision points and are easy to skim past, and both are load-bearing:** *Discovery verified by* (the literal command you run in Steps 6c and 9) and *Subagents / per-task model choice* (which gates Step 6b item 5). If the record is missing, ask the user rather than guessing.
>
> `<skills>` should have been replaced with a real path when this skill was installed. **If you are still reading the literal text `<skills>` below, it wasn't** — the index is the `README.md` sitting one level above this skill's own folder. Find it, use it, and replace the placeholders here so the next session doesn't repeat the hunt.

---

## Step 0: Should this even be a skill?

Gate it first. A skill is the right container only if the task **repeats** AND needs **packaged judgment or knowledge**. If not, name the better container and stop — don't scaffold a folder.

| If it's... | Build instead |
|---|---|
| A one-off task | Just do it now. No skill. |
| A single rule or preference ("always do X") | A line in the user's standing instructions file (`CLAUDE.md` / `AGENTS.md` / rules file). |
| A mechanical constraint enforceable by regex or a script | A hook, a lint rule, or a script. |
| A fixed prompt reused verbatim, with no branching | A saved prompt or slash command. |
| A repeating task needing judgment, voice, or structure | ✅ A skill — continue. |

State the call out loud: "This is a real skill — it repeats and needs [judgment / voice / structure]. Continuing." Or: "This doesn't need a skill — better as [container]. Want that instead?"

Two failure modes this gate exists to catch: a folder built for something done once, and a skill that is really just a preference the AI would follow from one line of instructions.

---

## Step 1: Get the description

If the trigger message already describes the skill clearly, skip this step — you have what you need.

Otherwise, one question: "What should this skill do? Plain English — what triggers it, what it does, what it produces."

---

## Step 2: Classify the tier (silent — no round-trip)

Read `references/tier-routing.md` now.

Match the description against the signals and set the tier **silently**. It sizes the work: how many questions, brainstorm or not, companion project folder or not. It is a derived label, not a decision to put to the user — the gating questions re-derive what it implies (Q3 sets Blueprints, Q4 sets Solutions, the novelty check gates brainstorming). Don't spend a round-trip confirming it; it surfaces once in the Step 5 spec block where the user confirms everything at once.

Hold the classification and continue.

---

## Step 3: Brainstorm (Tier 2/3, novel only)

Apply the **Brainstorming** section of `references/tier-routing.md` — it holds the conditions and the fallback. In short: delegate to a structured brainstorming skill if one is installed; if not, run the three inline questions given there yourself; skip entirely for Tier 1 or when the user gave a clear analogy ("basically X but for Y").

**Seam rule:** whatever brainstorming settles — task, inputs/outputs, behavior, structure — is decided. Brainstorming is the *open* exploration; the Step 4 questions only fill the gaps it left. Never re-ask what brainstorming already answered. Double interrogation is the fastest way to burn a user's patience.

---

## Step 4: The DBS questions

Read `references/dbs-questions.md` now.

Ask from the tier's question set — one at a time, waiting for each answer, unless the install record says the user chose batch or infer-and-confirm mode. **Skip any question already answered by the trigger message or by brainstorming.**

- All tiers: Q1a, Q1b, Q2–Q6
- Tier 2 adds: Q7, plus Q7b whenever the skill writes anything that persists
- Tier 3 adds: Q7 + Q7b + Q8–Q10

Track every answer — the spec block and the build both come straight out of them.

---

## Step 5: Present the spec block

Present the completed spec and wait for confirmation. Never build before this is confirmed — "it's simple" is not a reason to skip it.

```
── SPEC ────────────────────────────────────────
Skill name:     [kebab-case-name]
Tier:           [1 / 2 / 3]
Trigger:        [what the user says to invoke it]

Direction:      [one-sentence task summary]
Blueprints:     [list of references/ files] — or: none
Solutions:      [list of scripts/] — or: none

Behavior:       [silent / interactive]
Edge cases:     [handling strategy — or: n/a for Tier 1]
Install path:   [confirmed folder]
Discovery:      [how the AI will find it — from the install record]
─────────────────────────────────────────────────
```

Revise any field the user corrects, then confirm again. The spec block is where a wrong tier, a missing reference, or a bad name gets caught for free — after the build, each of those costs a rewrite.

---

## Step 6: Build

**This skill writes the files itself.** There is no external builder to hand off to.

### 6a — Companion project folder (only if the install record says yes, and only for the tiers it names)

Create the companion folder at the path named in the install record's **Companion project folder** row, as `<that path>/<skill-name>/`, containing:

```
README.md    what it is, why it exists, what success looks like
STATUS.md    current state, next step
DESIGN.md    the DBS answers written up — task, I/O, layers, behavior, edge cases

Tier 3 adds:
PLAN.md      phased build checklist
sessions/    append-only session logs
artifacts/   outputs the skill produces
```

Populate `DESIGN.md` from the Step 4 answers before writing the skill folder — writing the design down is what catches the contradiction between what the user said in Q2 and what they said in Q7.

### 6b — Write the skill folder

```
<skills>/<skill-name>/
├── SKILL.md              ← Direction (always required)
├── references/           ← Blueprints (only if Q3 = yes)
└── scripts/              ← Solutions (only if Q4 = yes)
```

Read `references/skill-anatomy.md` before writing `SKILL.md` — it holds the portable format rules (frontmatter fields and their limits, the name↔folder rule, file-size limits, the reference-depth rule). Then:

1. **Create the folder.** The folder name must be exactly the skill's `name` value — lowercase letters, digits and hyphens only. If the install record says folders take an ordering prefix, apply it to the folder *and* read the prefix rule in `references/skill-anatomy.md`: the number never appears in the frontmatter `name`, and the entry in the discovery directory is always the bare name.
2. **Write `SKILL.md`.** If a `SKILL-TEMPLATE.md` sits in the skills folder, start from it — it carries the house style, and starting from scratch each time is how two skills end up looking nothing alike. Structure either way: frontmatter (`name` + `description`) → 2–3 sentence overview → the step-by-step workflow → pointers to `references/` and `scripts/` at the steps that need them → hard rules at the end. Under 500 lines.
3. **Write each `references/` file** — only if Q3 = yes. One topic per file. **First check whether that knowledge already exists somewhere in the user's workspace** (a voice guide, a business facts file, an existing skill's reference). If it does, point at it — don't recopy it, or the two drift apart and nobody knows which is current.
   The same rule applies to the skill's own hard rules: if a constraint already lives in the user's standing instructions file, it applies to this skill anyway — don't restate it. Restating it means that when they change it in one place, the skill quietly keeps enforcing the old version. Only rules *specific to this task* belong in the skill.
4. **Write each `scripts/` file** — only if Q4 = yes. A script earns its place only when the output must be byte-identical every run or an API must be called; anything the AI can reason out belongs in `SKILL.md`.
5. **Tier 3, if Q9 surfaced mechanical steps:** if the platform supports subagents with per-task model choice, mark those workflow steps in the new `SKILL.md` for cheap-model dispatch ("dispatch a subagent on your cheapest model for this step — no judgment needed"). If it doesn't, write those steps as ordinary inline steps and say nothing about subagents — a skill that instructs a capability the platform lacks stalls at that line.

**Writing the `description` — the field that decides whether the skill ever fires.**
AIs under-trigger skills, so write it slightly pushy. Third person, start with "Use when…", then pile in concrete trigger phrases, symptoms, file types and synonyms the user would actually say. Describe **when** to use it — never the workflow steps.
- ✅ "Use when the user wants X, says 'phrase A', 'phrase B', or drops a Y file."
- ❌ "Does X by first doing A, then B, then C." — summarizing the workflow makes the AI follow the description and skip the body.

### 6c — Install it where the platform finds it

Follow the install record's discovery choice. The four shapes it can take:

- **A native skills directory** — put the folder there (or, if the canonical copy lives in the workspace, symlink it there under the bare skill name).
- **A routing line** — the skill folder stays in the workspace, and you **add a line to the instructions file the install record names**, pointing at this skill's `SKILL.md` path and listing the same trigger phrases as its description. Use the file the record names; don't invent a new one per skill unless the record says that's the convention. A skill nothing routes to is invisible, so this step *is* the install.
- **Both** — do both; the redundancy is cheap.
- **Paste-in** — no install action beyond filing it where the user can find it from the index.

Then **verify the skill actually resolves** — an unresolvable install is the single most common way a finished skill silently never runs. `references/skill-anatomy.md` has the per-platform paths and the verification command for each.

On a paste-in install (no discovery directory at all) there is nothing to resolve; the equivalent check is that the user can find the skill from the index and paste it. Say which check you ran — don't report "verified" without naming what you verified.

---

## Step 7: Generate test prompts

Using the trigger phrase, the task description and the I/O from the spec, generate 2–3 realistic prompts — the actual phrases the user would say on a normal workday. Not "test my skill." Not "run a test."

"Here are 3 test prompts based on the spec. Edit any that don't feel right:
1. […] 2. […] 3. […]"

Wait for approval or edits before running. A test prompt that says "test the skill" proves nothing: the real question is whether an ordinary sentence pulls the skill in.

---

## Step 8: Test it — two gates

A skill usually can't be invoked in the **same session** it was installed: most platforms load skill discovery at session start. So testing splits in two.

**8a — Dry-run (you run it now).** Run the gate the way the install record specifies (fresh subagent, fresh chat the user drives, or self-review as a last resort).

**What the cold reader gets** depends on whether it can open files, and this changes what the gate can prove:

- **Cold reader has file access** (a subagent, or a session pointed at the workspace): give it the new `SKILL.md`, one approved prompt, and the *paths* of everything else — references, standing-instruction rules, any workspace file the skill reads. **Don't paste those files in.** Let it open them itself, at the step that tells it to. This is the only configuration in which checklist item 2 means anything: you're watching whether it defers loading until the workflow says to.
- **Cold reader cannot open files** (a plain chat window): you have to inline the reference content, which means it has read everything before reaching the step that says "read it now." **Checklist item 2 is then unfalsifiable — mark it "not testable in this configuration" and say so.** Do not report it as passed. A check that can only come back "yes" or "can't tell" is not a check, and quietly ticking it is how a skill with mis-ordered reference pointers ships.

Either way: **nothing from the build conversation** — not the spec block, not the interview answers, not your reasoning. Those are what you're testing the skill can survive without. And always name any workspace file the skill reads, or the cold reader hits a path that doesn't exist for it and reports a dead reference that isn't one.

**On a self-review gate** (no subagent, no second session): report all four items as **weakened, not cold**. You are grading your own work with the whole build still in your head, which is exactly the state in which a missing step looks obvious to you and invisible to a stranger. Say so in the result rather than reporting four clean passes.

If the gate runs in a session the *user* drives, assemble that payload into one file yourself (`_dryrun-<skill-name>.md`, prompt at the bottom), hand over the path, and tell them to paste the whole file and add nothing else. Delete it once the gate passes. **Put it somewhere the platform does not scan for skills** — if the skills folder *is* the discovery directory, write it to the workspace root or a scratch folder instead; a stray markdown file in a discovery directory is at best noise and at worst a malformed skill. Never ask the user to work out what to paste — that is how the build conversation leaks in and the gate quietly stops being cold. Check all four:

- Does the workflow execute in the correct order — no dead references, no dead ends?
- Do the `references/` and `scripts/` load at the steps that need them — and not before? *(Only answerable when the cold reader could open the files itself. If you had to paste their contents in, it has read everything before reaching the step that says to: report this item **untested**, never passed.)*
- Does the output match what the spec described?
- **Value check:** would an AI *without* this skill have produced roughly the same thing? **Run the baseline, don't imagine it:** give the same prompt to a second cold reader with no skill attached, and compare the two outputs side by side. If you can't run a second reader, report this item **untested** rather than passing it — judging your own skill against an imagined alternative always flatters the skill. If the two outputs are close, the skill isn't earning its load: the Blueprints or the rules aren't doing real work. Tighten them, or reconsider whether Step 0 was right.

**8b — Acceptance (the user runs it).** The only true test of invocation: the user opens a **fresh session**, types one approved prompt the way they'd say it on a normal workday, and confirms the skill (a) is picked up — automatically from its description, or by whatever explicit invocation the install record names — and (b) followed cold, produces the right result. The dry-run cannot test this; the fresh session is what proves the skill actually loads.

Flag any issue with a specific fix. Apply it and re-run the affected gate — not both.

---

## Step 9: Ship

1. Confirm the skill is at the install path with the right DBS structure, and that discovery resolves (Step 6c's verification).
2. **Register it in the skills index** (`<skills>/README.md`): one row — name, one-line purpose, tier, trigger phrase. An unregistered skill is one nobody else on the team knows exists, including a future session of your own AI.
3. **Retire whatever this skill replaced.** If it came from a saved prompt, a snippet or an older skill, replace that file's contents with a single pointer line ("Superseded by the `<skill-name>` skill — see `<path>/SKILL.md`"). Don't delete it and don't leave the old version intact: retired files still get opened, so a pointer redirects whoever goes looking, while a stale original quietly stays the source of truth and a deleted one just gets rewritten from memory.
4. **Check trigger-phrase parity.** The phrases in the skill's `description` and the phrases in any routing line must be the same phrases. They drift the moment one is edited alone, and the symptom — a skill that fires sometimes — is miserable to diagnose. Read both, out loud, side by side.
5. If a companion project folder exists: update its `STATUS.md` to "shipped" and append a session log — date, skill name, tier, DBS layers used, decisions made.
6. Announce: "`<skill-name>` is live at `<path>`. Structure: SKILL.md [+ references/] [+ scripts/]. Fresh session to acceptance-test it."

---

## Editing an existing skill

Improving a skill that already exists skips the funnel — no Step 0, no tier questions, no spec block. The loop is:

1. **Read the whole skill first** — `SKILL.md` plus every `references/` and `scripts/` file. Never edit from the description alone; the thing you're about to add is often already three lines down.
2. **Edit**, judging every change against the same standard this skill builds to: DBS separation, description quality (Step 6b), pointers not copies, under 500 lines.
3. **Re-run the dry-run gate (8a)** for any change touching workflow order, reference pointers, the description, **or the content of a reference the skill relies on** — a Blueprint edit changes the output as surely as a step edit does, and it's the change most often waved through untested. Typo-level fixes skip it.
4. **Re-verify discovery** (Step 6c) if the folder was renamed or moved.
5. **Description changed?** Tell the user it only re-registers in a **fresh session** — 8b applies again.

---

## Hard rules

- This skill builds the files itself — never wait on or call an external skill-builder.
- Never build before the spec block is confirmed. Never skip the spec block, at any tier.
- Never skip Step 0. Not every request should become a skill.
- One skill per task. Two unrelated jobs = two skills.
- Skill names: lowercase letters, digits and hyphens only; 1–64 characters; no leading, trailing or doubled hyphens.
- `SKILL.md` only inside a skill folder — never a `README.md` there; the frontmatter description is the skill's front door.
- `SKILL.md` stays under 500 lines. Longer means reference material belongs in `references/`.
- Description: 1–1024 characters, no XML angle brackets.
- Reference files sit one level below `SKILL.md` — never a reference that points to a reference that points to a reference.
- Every skill gets registered in the skills index, and its install gets verified — the discovery path resolves, or on a paste-in setup the user can find it from the index. An installed-but-undiscoverable skill is a skill that does not exist.
- Test prompts must be realistic phrases from a normal workday.
