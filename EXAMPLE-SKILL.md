# EXAMPLE — one skill, request to shipped

A worked example, so the decision points in `IMPLEMENT.md` get answered against something concrete. This is a Tier 2 skill: it needs knowledge the AI doesn't have (a house format and a voice), but no code. It's the most common shape a first skill takes.

Two things before the walkthrough: a request that **correctly failed the gate**, because that's the part people skip, and then the one that passed.

---

## The request that failed the gate

> "Can you make me a skill that always uses British spelling in everything you write for me?"

**Step 0 verdict — not a skill.** It doesn't repeat as a *task*; it's a standing preference that applies to every task. There's no judgment to package and no knowledge to load. Put in a skill folder it would only fire when something matched its description, which means it would silently not apply most of the time — the opposite of what was asked.

**What got built instead:** one line in the standing instructions file. Thirty seconds, and it applies everywhere.

The gate saying no is a success. A skill library where nothing fails the gate is a folder of things nobody uses.

Other common no's: *"a skill that renames files to a date format"* → a script. *"a skill that stops you writing em dashes"* → a rule in the instructions file, or a lint hook if it must be enforced. *"a skill to migrate this database this weekend"* → that's just work; do it.

---

## The request that passed

> "Every Friday I write the same update to my team and I hate it. I've got notes all over the place and I end up rewriting the same four sections. Can you just do this?"

**Step 0 verdict — a real skill.** It repeats weekly, and it needs packaged knowledge: the section format they've settled into, and their voice. Said out loud: *"This is a real skill — it repeats and it needs your format and your voice. Continuing."*

### Tier (set silently, never asked)

Tier 2. It's not mechanical — it needs the house format and voice matching, which is an automatic Tier 2 floor. No API calls, no files written to disk, so not Tier 3. That sets the question list (base plus Q7) and, under DP-3, whether it gets a companion project folder.

### The interview

DP-6 was set to "one question at a time" at install, and the opening message was three sentences, so the funnel ran the full base set. Q1a and Q2 were already half-answered by the request, so they were confirmed rather than asked cold.

| Question | Answer | What it decided |
|---|---|---|
| **Q1a** Task | "Turn my week's notes into the Friday team update." | The one-sentence Direction. |
| **Q1b** Trigger phrases | "weekly update", "write the Friday update", "wrap up the week" | Seeds the description — which gets written richer than this at build time. |
| **Q2** Input / output | In: messy notes, a ticket list, sometimes chat threads. Out: a message, four sections, one screen, pasted into the team channel by hand. | Confirmed no file output → not Tier 3. |
| **Q3** Blueprints | "Yes — there's a format I've settled on, and it has to sound like me, not like an AI." | **Two reference files:** `update-format.md`, `voice.md`. This is the answer that made it Tier 2. |
| **Q4** Solutions | "No — nothing to calculate, nothing to send." | No `scripts/`. |
| **Q5** Behaviour | "Draft it and show me. Don't interview me for twenty minutes on a Friday." | Silent run, one review question at the end. |
| **Q6** Install location | The default skills folder. | Standard install. |
| **Q7** Edge cases | "If a week was quiet, say it was quiet. Don't pad it. And if my notes contradict each other, ask me — don't guess." | The Edge cases section, and the "never invent progress" hard rule. |

Note what Q3 did: it turned a vague "make it sound like me" into two named files. Without that question the skill would have been written with the format inlined in the body, and the voice left to chance — which is exactly the skill that fails the value check later.

### The spec block

```
── SPEC ────────────────────────────────────────
Skill name:     weekly-update
Tier:           2
Trigger:        "weekly update" / "write the Friday update" / "wrap up the week"

Direction:      Turn a week of raw notes into the team update, in format, in voice.
Blueprints:     references/update-format.md, references/voice.md
Solutions:      none

Behavior:       silent — drafts, then asks one review question
Edge cases:     quiet week → say so, never pad. Contradictory notes → ask, never guess.
Install path:   <skills>/03 weekly-update/
Discovery:      symlink → <discovery dir>/weekly-update  (DP-1 = A, DP-2 = B)
─────────────────────────────────────────────────
```

Confirmed with one correction — the user wanted "slipped" to lead when anything slipped, which went straight into the body as a rule. That correction cost one line at spec time. Found after the build, it would have cost a rewrite of the draft step and another test cycle.

### What got written

`SKILL.md`, 51 lines — well inside the 500-line ceiling, and typical: most Tier 2 skills land between 40 and 120 lines, and a first draft over 200 usually means reference material is sitting in the body.

```markdown
---
name: weekly-update
description: Use when the user needs to write the Friday team update, a weekly status note, or a "here's where we landed this week" message. Triggers — "weekly update", "write the Friday update", "team update", "what do I send the team", "wrap up the week", or when the user pastes a week's worth of notes and asks for the summary. Also use when editing a draft update before it goes out.
---

Turns a week of raw notes, tickets and messages into the update the team actually reads: what shipped, what slipped, what's next, and the one decision that needs someone else. Written in the user's voice, in the house format, short enough that people finish it.

## Step 1: Gather the week

Collect the inputs the user points at — notes, ticket lists, chat threads, calendar. If nothing is provided, ask once: "What have you got for this week — notes, tickets, anything?" Don't go hunting through the workspace unprompted.

Missing or thin input is normal on a bad week. Draft from what exists and mark the gaps as `[nothing this week?]` rather than inventing progress — a fabricated line in a status update is the one error that costs trust permanently.

## Step 2: Sort into the four buckets

- **Shipped** — done and visible to someone outside the team.
- **Slipped** — expected this week, didn't land, with the actual reason.
- **Next** — the two or three things that matter next week. Not everything.
- **Needs a decision** — anything blocked on someone who reads this update.

Anything that fits none of the four is noise. Cut it.

## Step 3: Read the format and the voice

Read `references/update-format.md` for the section order, length ceilings and the rules about what never goes in.

Read `references/voice.md` before writing a word. The update sounds like the user, not like an AI writing on their behalf — that reference is the difference between an update people read and one they scroll past.

## Step 4: Draft

One pass, in format, in voice. Hard ceiling: the whole update fits on one screen.

Lead with Slipped if anything slipped. Good news at the top of a week where something went wrong reads as spin.

## Step 5: Hand it over

Present the draft in full and ask one question: "Anything wrong or missing?" Apply edits and hand back the final. Never send it anywhere — the user sends it.

## Edge cases

- **A week with nothing shipped:** say so in one line and move to Next. Padding an empty week is worse than a short update.
- **Conflicting notes** (a ticket says done, a message says blocked): surface both to the user, don't pick. One line: "Notes disagree on X — which is it?"
- **Something sensitive in the raw notes** (a personnel matter, an unannounced decision): leave it out of the draft and flag it separately to the user.
- **A step fails twice:** stop, say what's missing, hand back what you have.

## Hard rules

- Never invent progress. A gap gets marked as a gap.
- Never send, post or publish the update. Draft only.
- One screen. If it doesn't fit, cut Next before cutting Slipped.
- Voice reference is not optional — it loads before drafting, not after.
```

Plus the two Blueprints:

- `references/update-format.md` — the four sections, the order, the one-screen ceiling, what never goes in (headcount changes, anything not yet announced).
- `references/voice.md` — how the user actually writes. **Check first whether this already exists in the workspace.** If there's a voice guide anywhere, the skill points at it instead of holding a second copy — two voice guides drift, and nobody knows which one is current.

Read the description again and notice what it does *not* do: it never says "first gathers notes, then sorts them into four buckets." A description that summarises the workflow gets followed *instead of* the body. It lists when to use the skill and the words the user would really say — including "what do I send the team", which they'd never think to type as a trigger phrase but would absolutely say.

### Install and discovery

DP-1 was A and DP-2 was B, so: the folder is `<skills>/03 weekly-update/`, and a symlink named `weekly-update` — bare, no number — goes into the discovery directory. Verified through the link, not around it:

```bash
ls -lL "<discovery dir>/weekly-update/SKILL.md"
```

Then registered in `<skills>/README.md`: name, one-line purpose, tier, trigger phrase.

### Test prompts

Generated from the spec, then approved by the user:

1. "Can you do the Friday update? Notes are in this week's file."
2. "Wrap up the week for me — here's what I've got." *(with notes pasted)*
3. "Weekly update, but it was a quiet week."

The third exists on purpose: it tests the edge case from Q7. Test prompts that only exercise the happy path prove the least interesting thing about a skill. And note that none of them says "test the skill" — the question is whether an ordinary Friday sentence pulls the skill in.

### Dry-run (DP-4 = A: a fresh subagent, cold)

The subagent got the `SKILL.md` and prompt 2, nothing else. All four checks:

1. **Order** — clean; every step ran, no dead references.
2. **References load at the right step** — ✅, but it flagged that the voice reference is pointed at in Step 3, *after* the buckets are sorted. Fine, because nothing is written until Step 4. It would be a bug if drafting started earlier.
3. **Output matches the spec** — four sections, one screen, ✅.
4. **Value check** — the honest one. Without the skill, an AI asked to "write a weekly update" produces four plausible sections in generic corporate voice. With it: the house section order, the one-screen ceiling, the slipped-leads rule, the never-pad rule, and the user's actual voice. **The skill earns its load — and specifically, `references/voice.md` is where most of that difference lives.** If the value check had come back "an unassisted AI would have written roughly this", the fix is not a longer `SKILL.md` — it's a real Blueprint or a real rule, or an honest admission that Step 0 should have said no.

One fix applied: the first draft had no instruction against sending the update anywhere. The subagent's output ended with an offer to post it. Added to the hard rules, dry-run re-run, clean.

### Acceptance (the user, in a fresh session)

The skill was installed mid-session, so it could not fire in that session — most platforms load skill discovery at start-up. The user opened a new session on Friday and typed prompt 1, in their own words.

It fired, and the draft was right except for one section order. That's a body fix, not a description fix, so: edit the body, re-run the dry-run only, done. **If it had not fired at all, the description would have been the problem** — that's the split worth remembering. Didn't fire → description. Fired but wrong → body.

---

## Translating this to other skill shapes

| If your skill is… | What changes |
|---|---|
| **Tier 1** — "pull the action items out of a transcript" | Q3 and Q4 are both no, so no `references/`, no `scripts/`, no companion project folder, no brainstorm. `SKILL.md` alone, usually under 40 lines. Everything else in the funnel is identical — including the spec block, which is not optional just because the skill is small. |
| **Tier 3** — "enrich a lead list from a CRM API and write a report" | Adds Q8 (which API, what auth, what the response looks like), Q9 (which steps are mechanical enough for a cheap-model subagent — only ask if the platform supports that), Q10 (what a successful run looks like, what failure looks like). Adds `scripts/`. If it's genuinely novel, brainstorming runs before the questions. The dry-run has to actually run the scripts, not read them. |
| **A voice-only skill** — "reply to inbound emails the way I would" | Tier 2 by the voice override regardless of size. The whole value sits in one Blueprint, which makes the value check the gate that matters: if an unassisted AI writes a comparable reply, the voice guide isn't specific enough yet. |
| **A skill that fails the gate halfway through the interview** | It happens — Q2 reveals the output is always identical, which means it's a template, not a skill. Stop and say so. The sunk cost of four questions is not a reason to build a folder. |
