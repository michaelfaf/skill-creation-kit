# IMPLEMENT — Skill Creation install walkthrough

> **To the AI running this:** this file is your script and `STATUS.md` is your memory — update it after every scan, decision and phase, not at the end. A human can follow this file by hand; every step is doable without an AI. Present decisions conversationally, one at a time, and never pick for the user. Adapt to their stack; never transplant a path or a name that doesn't fit it. **Set the expectation when you confirm the go-ahead:** the install is ~20 minutes; Phase 3 builds one real skill with them and takes 30–60 minutes more. One sitting, or two with `STATUS.md` as the bridge.

## What you are installing

A **skill** is a folder holding a `SKILL.md` — a written procedure your AI loads when a matching request comes in, so a workflow the user has explained five times gets explained once and then just happens. This kit installs a skill that **builds those skills**: a funnel from "we keep doing this by hand" to a tested, installed, registered skill.

| Piece | Job |
|---|---|
| **The gate** | Before anything is built: does this even deserve to be a skill? A one-off is just work. A single preference is a line in your instructions file. A mechanical constraint is a hook. A fixed prompt is a saved command. Only a *repeating task needing packaged judgment or knowledge* is a skill. Most requests fail this gate, and that's the point. |
| **DBS** | The structural model. **D**irection = `SKILL.md`, the workflow — what the AI should DO. **B**lueprints = `references/`, knowledge it lacks — what it should KNOW. **S**olutions = `scripts/`, code for output that must be identical every run. Every question in the funnel exists to decide which layers this skill needs. |
| **Tiers** | A silent size label (1 simple / 2 needs knowledge or voice / 3 calls APIs or writes files) that routes how much process the build gets. Derived, never put to the user as a question. |
| **The interview** | Six base questions, up to ten for complex skills, asked one at a time, each one deciding a layer or a behaviour. Skips anything the user already told you. |
| **The spec block** | A short confirm-before-build summary. Catches a wrong name, a missing reference or a mis-sized skill for free — after the build each of those costs a rewrite. |
| **Install and discovery** | Writing the folder is the easy half. Getting the platform to actually *find* the skill — and proving it resolves — is where skills silently die. |
| **Two test gates** | A **dry-run** (someone with no context follows the new `SKILL.md` cold, including a value check: would an AI without this skill have produced the same thing?) and an **acceptance** run in a fresh session, because most platforms load skill discovery at session start and cannot test their own registration mid-session. |
| **The index** | Every skill registered in one table, so the library stays known and skills stop being rebuilt by accident. |

The payload is `skill/` (the skill itself — `SKILL.md` plus three references, in the portable agent-skill format) and `templates/` (the skills index and a `SKILL.md` skeleton you install). Two companion docs: `OVERVIEW.md` explains the concepts for the human; `EXAMPLE-SKILL.md` walks one real skill from request to shipped.

**Vocabulary used below.** *Workspace* — wherever the user's work actually lives: a notes vault, a project folder, a git repo. Not loose files in Downloads. *Discovery directory* — the specific folder a platform scans for skills at session start. *Install record* — the block in the skills index that remembers the six choices below, so future sessions build the same way.

## Phase 0 — Environment scan

Before any questions, establish and record in `STATUS.md`'s scan table:

1. **What am I?** Platform and capabilities: can I read and write files? run shell commands? create symlinks? launch subagents? choose a cheaper model for a task? Do I have persistent memory across sessions?
2. **Does this platform have a native skills directory?** Test for it rather than listing it — a bare `ls dir 2>/dev/null` is silent whether the directory is empty or missing, and piping it (`| head`) throws the exit code away entirely. For a Claude Code-class agent:
   ```bash
   for d in ~/.claude/skills .claude/skills; do
     [ -d "$d" ] && echo "FOUND: $d ($(ls -1 "$d" | wc -l | tr -d ' ') entries)" || echo "absent: $d"
   done
   ```
   Substitute your platform's paths. If you're not sure whether your version supports skills at all, say so plainly rather than guessing — DP-1 has a path for "no native support".
3. **Where does the user's work live?** A notes vault, a project folder, a git repo, a shared team repo? Is there an existing `skills/`, `prompts/` or `commands/` folder — anything that is already doing this job informally?
4. **Existing conventions:** does the user have a standing instructions file (`CLAUDE.md`, `AGENTS.md`, `.cursor/rules/`, `GEMINI.md`, `.github/copilot-instructions.md`, a system prompt, a "start here" note)? **Read it.** Phase 2 merges into it, never overwrites. Note the naming style they already use — you'll match it, not replace it.
5. **Any skills already there?** If the user has skills, read one. It tells you their house style, and Phase 2 shouldn't install a second convention alongside a working one.

Report the scan back in three or four lines before moving on. If something is ambiguous — "I can't tell whether this Cursor version reads skills" — say that, and let DP-1 resolve it rather than guessing and installing into a folder nothing reads.

**Chat-only fallback:** no file access → you narrate every step, the user creates the folders and files, and `STATUS.md` lives in a note they paste back at the start of each session. Everything downstream still works; skills become documents the user pastes, and DP-1 option D is written for exactly this.

## Phase 1 — Decisions

Before the first one, orient the user with `EXAMPLE-SKILL.md` — one real skill walked from request to shipped, plus one request that correctly *failed* the gate. Two minutes, and every decision below gets answered against a concrete picture instead of an abstraction.

Work through these in order. Record each in `STATUS.md`'s decisions table before moving to the next.

### DP-1 — Where do skills live, and how does your AI find them?

**Options:**
- **A. Canonical in the workspace, linked into the discovery directory** — the skill folders live with the user's other work (`<workspace>/skills/`), and a symlink puts each one where the platform scans. *Trade-off:* needs a shell and symlink support; if the platform doesn't follow links, this silently half-works.
- **B. Canonical in the platform's discovery directory** — skills live only where the platform looks (e.g. `~/.claude/skills/`). *Trade-off:* the skills sit outside the user's workspace, so they're not backed up, versioned or visible with the rest of their work.
- **C. Canonical in the workspace, routed by an instructions line** — for platforms with no skills directory: the folders live in the workspace and the standing instructions file says "when the user asks about X, read `<path>/SKILL.md` and follow it." *Trade-off:* the routing line is manual and grows with every skill; past ~10 skills it needs an index line instead of one line per skill.
- **D. Documents the user pastes** — chat-only platforms. Skills live in a folder the user can reach; they paste the relevant `SKILL.md` at the top of a session. *Trade-off:* invocation is entirely on the user's memory, which is what the index is for.

**Recommendation:** condition on the scan. **Native skills directory + shell + a real workspace → A** — one copy, edits are live, and the skills travel with everything else the user backs up. **Native skills directory but no shell, or a platform that won't follow symlinks → B.** **No native skills directory but files and a workspace → C.** **No file access → D.** Never keep two copies in sync by hand; a stale duplicate is the second most common way a skill misbehaves, after a broken link.

If the user says "whatever you recommend": A if the scan found a native skills directory *and* a shell, B if it found the directory but no shell, C if it found no skills directory but the platform reads an instructions file, D if there's no file access. The default folder is `skills/` at the workspace root — any name is fine, record the one chosen; everything downstream calls it `<skills>`.

**A recipe (do it in Phase 2):** create `<workspace>/skills/`, then for each skill `ln -s "<absolute path to the skill folder>" "<discovery dir>/<skill-name>"` and verify with `ls -lL "<discovery dir>/<skill-name>/SKILL.md"` — the `-L` follows the link, so a wrong target errors instead of printing a plausible line. Absolute paths only; a relative symlink target resolves against the link's directory, not yours.

### DP-2 — Do skill folders take an ordering prefix?

**Options:**
- **A. Bare names** — `outreach-drafter/`. *Trade-off:* a folder of thirty skills sorts alphabetically and tells you nothing about what came when.
- **B. Numbered prefix on the storage folder** — `02 outreach-drafter/`, with the frontmatter `name` and the discovery-directory entry both staying bare. *Trade-off:* you must check the highest number before each build, and the number is meaningless the moment you'd want to reorder (you never renumber — links point at the folder).

**Recommendation:** **B if the user will browse the skills folder as a list** — which is the case when skills live in the workspace next to their notes (DP-1 = A or C), and it's the kit originator's choice: the numbers are creation order, so the folder doubles as a build log. **A if skills live only in a discovery directory the user never opens** (DP-1 = B) — there, the number buys nothing and risks the spec violation below. Default if the user shrugs: B when DP-1 is A or C, A when DP-1 is B or D.

**The rule that makes B safe, either way:** the number lives on the *storage* folder only. The frontmatter `name` is always bare, and the entry in the discovery directory is always bare. The portable skill spec requires the folder name to equal the `name` — a numbered folder placed directly into a discovery directory breaks that, and the skill may simply never load. `skill/references/skill-anatomy.md` states this; if the user picks A, that section still applies to nothing and does no harm.

### DP-3 — Does a complex skill get a companion project folder?

A companion folder (`README` + `STATUS` + `DESIGN`, and for the biggest builds `PLAN` + `sessions/` + `artifacts/`) sits outside the skill and holds the *build* record: why it exists, what was decided, what's left. The skill folder holds the procedure; the project folder holds its history.

**Options:**
- **A. Never** — the skill folder is the whole artefact. *Trade-off:* six months later nobody remembers why a rule in the skill exists, so it gets "cleaned up" and something breaks.
- **B. Tier 3 only** — only skills with scripts, APIs or file output get the folder. *Trade-off:* a heavily-iterated Tier 2 skill (a voice-matching one, typically) has nowhere to record what was tuned.
- **C. Tier 2 and 3** — the kit originator's setup. *Trade-off:* two folders per skill, and if nobody reads `STATUS.md` they're just clutter.

**Recommendation:** **C if the user already keeps project folders with status files** — the habit exists, the folder gets read, and voice-tuned skills are exactly the ones whose decisions get forgotten. **B if they don't** — start where the payoff is unambiguous, and add Tier 2 later if they find themselves wanting it. **A only if the user is solo, building a handful of simple skills, and says outright they won't read the folder** — an unread status file is worse than none, because it looks authoritative while going stale. Default: C if the scan found existing project/status folders, otherwise B. Record the parent path; the default is `projects/<skill-name>/` in the workspace.

### DP-4 — How does the dry-run gate run?

Every skill gets tested twice: a **dry-run** (someone follows the new `SKILL.md` cold, before the user ever relies on it) and an **acceptance** run in a fresh session. The acceptance run is always the user's. The dry-run is the one with options.

**Options:**
- **A. A fresh subagent** — you launch one, hand it only the new `SKILL.md` and one approved test prompt, and read its output. *Trade-off:* needs a platform that runs subagents.
- **B. A fresh session the user drives** — the user opens a new chat or composer, pastes only the new `SKILL.md` and one test prompt, and brings the result back. *Trade-off:* a real stop; you wait for them. It is also the *better* test — a genuinely separate context, not a child of yours.
- **C. Self-review in the same session** — you re-read the `SKILL.md` you just wrote against the checklist and report. *Trade-off:* you are grading your own work with the whole build still in your context, which is exactly the condition under which a missing step looks obvious to you and invisible to a cold reader.

**Recommendation:** **A if the platform has subagents** — it's free and instant, so it actually gets run. **B if not** — and treat it as a genuine stop: tell the user exactly what to paste and what to ask, then wait. **C only when neither is possible** (and say out loud that the gate is weakened). Default: A if the scan found subagents, else B.

**The checklist, whichever option runs it** — all four, every time:
1. Does the workflow execute in the correct order, with no dead references and no dead ends?
2. Do the `references/` and `scripts/` get loaded at the steps that need them — and not earlier?
3. Does the output match what the spec block described?
4. **Value check:** would an AI *without* this skill have produced roughly the same output? If yes, the skill isn't earning its load — the Blueprints or the rules aren't doing real work. Tighten them, or accept that the gate in Step 0 should have said no.

### DP-5 — How does a skill get invoked?

**Options:**
- **A. Automatic from the description** — the platform matches the user's message against every skill's description and loads the match. *Trade-off:* all the weight falls on description quality; a vague description means a skill that exists and never fires.
- **B. Explicit** — the user names it (`/skill-name`, `@skill`, "use the outreach skill"). *Trade-off:* it only fires when the user remembers it exists.
- **C. Both** — a pushy description *and* a registered command or a memorable name. *Trade-off:* two things to maintain per skill.
- **D. Paste-in** — chat-only; the user pastes the skill at the start of a session.

**Recommendation:** **C wherever the platform supports both** — automatic triggering is what makes a skill feel like a capability rather than a tool, but an explicit fallback is what saves the day when it doesn't fire and the user knows the skill exists. **A if the platform only matches descriptions, B if it only takes explicit invocation, D if chat-only.** Default: C if the scan found both, else whichever single one exists.

Whichever you pick: the **description discipline in `skill/SKILL.md` Step 6b applies anyway.** Under-triggering is the normal failure mode — descriptions get written as summaries of what the skill does, when they need to be lists of when to use it. And on any platform that loads skills at session start, tell the user now: a skill just installed will not fire in *this* session. That's not a bug and it's why acceptance testing happens in a fresh one.

### DP-6 — How much interview before the build?

**Options:**
- **A. One question at a time** — ask, wait, ask the next. *Trade-off:* six round-trips before anything gets written.
- **B. All questions in one block** — the user answers in a single pass. *Trade-off:* a wall of questions gets skimmed, and skimmed answers are how a skill ends up with a `references/` folder nobody needed.
- **C. Infer and confirm** — draft the spec block straight from the user's opening description, present it, let them correct it. *Trade-off:* if the description was thin, the spec is a guess and the user is now editing your assumptions instead of stating their own.

**Recommendation:** **A when the request arrives as one line or a vague gesture** ("something for the weekly report thing") — the originator's default, because the answers are the build and shallow answers make a shallow skill. **C when the user's opening message already answers most of Q1–Q6** — the skill's Step 1 and Step 4 both say to skip what you already know, so a detailed brief should not be re-interrogated. B is the worst of both and isn't recommended. Default: A if the trigger message is under about three sentences, C if it's longer and already covers task, inputs and output. Either way the spec block is the real gate — it is never skipped, whichever mode ran.

## Phase 2 — Install

1. **Install the skill** at the DP-1 location, in a folder named exactly `skill-creation`:
   - **DP-1 = A:** copy `skill/` to `<workspace>/skills/skill-creation/` (with the DP-2 prefix on the folder if prefixes are on), then symlink it into the discovery directory under the bare name and verify with `ls -lL`.
   - **DP-1 = B:** copy `skill/` straight into the discovery directory as `skill-creation/`.
   - **DP-1 = C:** copy `skill/` to `<workspace>/skills/skill-creation/`; the routing happens in step 4.
   - **DP-1 = D:** the user keeps `skill/SKILL.md` and its three references as the documents they paste.
   - Adapt minimally. Only rename terms that clash with the user's existing vocabulary — if they call them "playbooks", say playbooks. Don't rewrite the procedure.
2. **Create the skills index:** `<skills>/README.md` from `templates/SKILLS-INDEX.md`. Fill it in **with the user** — the library table seeded with `skill-creation` itself, the conventions section matching what they actually chose, and the **Install record** completed with all six decisions. Never install it with placeholders left in; the record is how a session six months from now knows how skills get built here.
3. **Install the skill template** (optional, default yes): `templates/SKILL-TEMPLATE.md` → `<skills>/_template/SKILL.md`. Ask first; some users would rather the skill write from scratch every time.
4. **Wire the standing instructions file.** This is the stickiness step — an install that only creates folders dies at the user's first new chat. Merge into their existing file, in their format:

   > **Building skills.** Before building anything new, check `<skills>/README.md` — a skill may already cover it. When the user wants to automate a repeating workflow, turn a prompt into something reusable, or says "build me a skill", use the `skill-creation` skill: it gates whether the thing should be a skill at all, then scaffolds, installs and tests it. Every new skill gets registered in that index.

   For **DP-1 = C**, add the per-skill routing line the same place: "When the user asks about \<triggers\>, read `<path>/SKILL.md` and follow it."

   **Scope warning:** some platforms scope instruction files to a folder or workspace rather than the user globally — Cursor uses `.cursor/rules/*.mdc` (create `.cursor/rules/skills.mdc` with frontmatter `description: Skill library routing` and `alwaysApply: true`; without `alwaysApply` the rule may never fire), Codex-style agents use `AGENTS.md` in the repo root, Copilot uses `.github/copilot-instructions.md`. Put the wiring where the user's AI actually works, and **tell them which file it landed in.** If the user works across several repos, say plainly that the line needs repeating in each, or that it belongs in their global instructions file if the platform has one.
5. **Record every path installed** in `STATUS.md`.

## Phase 3 — First live build — on something real

Build **one real skill the user actually wants**, not a toy. This is the phase that proves the install and teaches the funnel at the same time.

1. **Pick it with the user.** Ask: "What do you explain to your AI more than once a month?" Steer toward something small — a Tier 1 or Tier 2 with one reference file. A first build that needs API credentials will stall on the credentials and teach nothing about the funnel.
2. **Run `skill/SKILL.md` end to end, out loud:** the Step 0 gate (say the verdict, don't just think it) → tier set silently → interview per DP-6 → **spec block, confirmed** → write the folder → install and verify discovery → generate realistic test prompts → **dry-run per DP-4, all four checklist items reported, including the value check** → register in `<skills>/README.md`.
   **If the Step 0 gate says no** — which happens, and is a success, not a failure — say which container it should be instead, build *that* (a line in the instructions file, a script, a saved prompt), then pick a different candidate and run the funnel again. The user needs to see the funnel complete once.
3. **Verification test — the acceptance gate (Step 8b), and a real stop.** The user opens a **fresh session**, types one of the approved test prompts the way they'd say it on a workday, and reports back two things: did the skill get picked up, and was the output right? Tell them exactly what to type and then wait — Phase 4 does not start until the answer is back. Record the prompt and a one-line summary of the result in `STATUS.md`.
   - It fired and the output was right → the install works.
   - It didn't fire (DP-5 = A or C) → the description is the problem, not the skill. Rewrite it per Step 6b — more trigger phrases, more of the user's actual words, no workflow summary — reinstall, and have them try again in another fresh session.
   - It fired but the output was wrong → the body is the problem. Fix the step that went wrong and re-run the dry-run gate only.
   - The platform has no automatic invocation (DP-5 = B or D) → the test is that the user can find the skill in the index and invoke it by name, and that it runs correctly cold.

## Phase 4 — Wrap

1. Walk the user through what's installed and where: the skill, the skills index, the instructions-file wiring (name the file), and the first skill they built.
2. Tick every phase in `STATUS.md`; empty the Blockers row or state what remains.
3. **Confirm the install record in `<skills>/README.md` is filled in** — all six decisions — before anything else. That block is the only thing that survives this session.
4. Leave the habit behind, one line: **"The third time you explain the same thing to your AI, that's a skill — and check the index before building, because it might already be one."**
5. The kit repo is now safe to delete, or keep it for re-reads. Nothing downstream depends on it.

## If things go wrong

- **A capability is missing** (no subagents, no shell, no skills directory, no file access): take the nearest degradation path named in the DP. Every step has a sequential, routed or paste-in form — nothing here dead-ends.
- **The user's conventions clash with the kit's names:** keep their names, map the jobs. The skills folder can be called anything; `references/` can be `docs/` if that's their habit. Two things are not negotiable because the format depends on them: the file is `SKILL.md`, and the folder name matches the frontmatter `name` at the point of discovery.
- **The skill doesn't fire in a fresh session:** in order — is the discovery path right (`ls -lL` through the link)? Does the folder name equal the `name`? Is the description written as *when to use it* rather than *what it does*? That third one is the answer more often than the first two combined.
- **The user wants a skill for something that failed the Step 0 gate:** build the right container instead and say why. If they insist, build the skill and note in the index that it was built over the gate — a library where the gate never says no is a library of folders nobody uses.
- **A step fails twice:** don't loop. Note it in `STATUS.md` Blockers, take the fallback, and tell the user.
