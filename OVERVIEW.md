# Overview — what this is, before you install it

Read this if you want to understand the system before running the installer. `IMPLEMENT.md` is the *how*; this is the *what* and the *why*. Ten minutes.

## The problem

You explain the same thing to your AI over and over. The format your team's updates take. The way you want a transcript turned into notes. The five checks you run before a proposal goes out. Each time you re-explain it, you explain it slightly differently, so you get slightly different output, and the version you liked best exists only in a chat you can't find any more.

The obvious fix is to save the prompt. That works until the task has any branching in it — an edge case, a decision, a "unless it's a Tuesday". Then a saved prompt is a paragraph you have to read, edit and re-paste every time, and you stop using it.

The other failure mode is the opposite: people discover skills, get excited, and build fifteen folders in a weekend. Twelve of them are things that should have been a single line of instructions or a script. They never fire, they clutter the library, and the three good ones get lost among them.

So there are two problems, and a system that only solves one makes the other worse: **capturing a workflow properly**, and **refusing to capture the ones that shouldn't be.**

## The core idea

A **skill** is a folder containing a `SKILL.md` — a written procedure your AI loads when a matching request comes in. Not a prompt you paste: a file the AI finds on its own, reads, and follows. The format is an open specification supported by a long list of agents, so a skill you write for one tool works in another.

This kit installs a skill whose job is **building other skills**, and the funnel it runs is opinionated in one specific way: it tries to talk you out of it first.

The order is: gate it → size it → interview → confirm the spec → write → install → test twice → register. Nothing gets scaffolded before the gate, and nothing gets written before the spec is confirmed.

## The concepts you'll meet

**The gate (Step 0).** Before anything else, one question: does this repeat, *and* does it need packaged judgment or knowledge? If not, the answer names a better container — a line in your instructions file for a standing preference, a script or hook for a mechanical rule, a saved command for a fixed prompt, or "just do it now" for a one-off. Most requests fail this gate. That's the feature.

**DBS.** The three layers a skill can have, and the reason the interview asks what it asks:
- **Direction** = `SKILL.md`. The workflow. What the AI should *do*. Always required.
- **Blueprints** = `references/`. Knowledge the AI doesn't have — your voice, your format, your pricing, your customer profiles. What it should *know*. Loaded only when the workflow reaches them, so they're free until used.
- **Solutions** = `scripts/`. Code, for output that must be byte-identical every run or for calls the AI can't make itself.

Most skills are Direction only or Direction plus Blueprints. A `references/` folder a skill didn't need is bloat; a missing one is a skill that confidently invents your format.

**Tier.** A size label — 1 simple, 2 needs knowledge or voice, 3 calls APIs or writes files — that the AI sets *silently* to route how much process the build gets. You never get asked "what tier is this?", because you'd have to learn the model to answer, and the interview re-derives it anyway.

**The interview.** Seven base questions, up to twelve for complex skills, asked one at a time. Each one decides something structural: Q3 decides whether there's a `references/` folder, Q4 decides whether there's a `scripts/` folder, Q7 decides the edge-case behaviour. Anything you already said in your opening request gets skipped.

**The spec block.** A ten-line summary presented before a single file is written: name, tier, trigger, layers, behaviour, install path. You confirm or correct it. This is the cheapest place in the whole process to catch a wrong name or a missing reference — after the build, each of those is a rewrite.

**Discovery.** Where your platform looks for skills, and whether it finds yours. Writing a good skill is the easy half; skills mostly fail by being installed somewhere nothing reads, or by having a description that never matches anything a human would say. The install verifies the path resolves, and the tests verify the description works.

**The two test gates.** A **dry-run**, where someone with no context — a subagent, or you in a separate chat — follows the new skill cold and reports back against four checks. And an **acceptance run** that you do in a *fresh session*, because most platforms load skill discovery at session start and therefore literally cannot test a skill they just installed.

The fourth dry-run check is the one people skip and shouldn't: **would an AI without this skill have produced roughly the same thing?** If yes, the skill isn't earning its place in your library — it's a folder that makes you feel organised.

**The index.** One table listing every skill: name, purpose, tier, trigger phrase. Checked before building anything new, so you stop rebuilding what you already have. It also carries an **install record** — the six choices you made during install — so a session six months from now builds skills the same way this one did.

## How a build actually flows

You say: *"I keep doing this by hand every week, can we make it automatic?"*

Your AI loads the skill and gates it — out loud, so you can disagree. Say it passes. The AI silently sizes it, then asks you seven questions, one at a time, skipping the two you already answered. It presents a spec block; you correct one field. It writes the folder — `SKILL.md`, plus a reference file if the interview said you need one — installs it where your platform looks, and verifies that path actually resolves.

Then it proposes three test prompts in your own words, and runs a cold dry-run on one of them. Say the dry-run reveals the skill never told the AI to stop before sending anything. That gets fixed and re-run.

It registers the skill in your index and tells you the last step is yours: open a fresh session tomorrow, type one of those prompts the way you'd really say it, and see whether the skill fires. If it fires and the output is right, you're done. If it doesn't fire, the description is wrong. If it fires but the output is wrong, the body is wrong. Those two failures have different fixes and it's worth knowing which is which.

Start to finish for a medium skill: under an hour, most of it the interview and the tests.

## What gets installed

- **The `skill-creation` skill** — `SKILL.md` plus three reference files (tier routing, the interview questions, and the portable-format rules) — wherever your platform discovers skills.
- **A skills folder and an index** — the library the skill registers into, with your six install choices recorded in it.
- **A line in your standing instructions file** so skill requests route here automatically. Without this, the install dies at your next new chat.
- **Optionally a `SKILL.md` template** for when you write one by hand.
- **One real skill of your own**, built with you during the install.

## Your role versus your AI's

Your AI does the scanning, the writing, the installing and the dry-run. You do four things: answer the interview honestly, confirm or correct the spec block, run the acceptance test in a fresh session, and — the important one — **accept a "no" at the gate.** The system's value comes disproportionately from the skills it talks you out of building.

## What it needs from your platform

Very little, and it degrades rather than dead-ends. Ideally: file access, a shell, a native skills directory, and subagents. Without subagents the dry-run becomes a fresh chat you run. Without a skills directory the skill lives in your workspace and your instructions file routes to it. Without file access it becomes documents you paste. Each of those is an explicit option in the installer, not an afterthought.

## What it is not

Not a prompt library — a saved prompt has no branching, no edge cases and no loaded knowledge. Not an agent framework — nothing here runs on a schedule or calls itself. Not a way to make your AI smarter in general; it makes your AI *reliable at the specific things you do repeatedly*, which is a different and more useful thing. And not a system that rewards volume: a library of six skills that all fire is worth more than thirty that mostly don't.

## Provenance, honestly

This was extracted from a working skill inside a personal knowledge system, in daily use, that has built a real library of skills for one operator and their team. Every rule in it exists because something went wrong without it: the gate exists because too many folders got built; the value check exists because some of them turned out to do nothing; the fresh-session acceptance test exists because a skill was declared shipped and then never fired for weeks.

What's here is the mechanism, with the original's paths, names and personal conventions stripped out and turned into the six choices the installer asks you. Where the original made a choice you might reasonably make differently, it's a decision point with the original reasoning stated as the recommendation — not a default silently baked in.
