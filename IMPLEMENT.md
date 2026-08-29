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

The payload is `skill-creation/` (the skill itself — `SKILL.md` plus three references, in the portable agent-skill format) and `templates/` (the skills index and a `SKILL.md` skeleton you install). Two companion docs: `OVERVIEW.md` explains the concepts for the human; `EXAMPLE-SKILL.md` walks one real skill from request to shipped.

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
   Substitute your platform's candidates. **Do not invent a path and install into it** — an install into a directory nothing reads is the failure this whole kit exists to prevent. If the probe finds nothing, that is a finding: record "none" and let DP-1 route you to option C.

   **Codex CLI, Gemini CLI, Copilot and any other AGENTS.md-style agent:** probe `~/.codex/skills`, `.codex/skills` and `.agents/skills` for completeness, but expect none — these agents route through a single instructions file (`AGENTS.md`, `GEMINI.md`, `.github/copilot-instructions.md`) rather than scanning a skills directory. Go to DP-1 option C. That is the designed path, not a downgrade.

   **Cursor specifically:** skills-directory support is version-dependent, so probe `~/.cursor/skills` and `.cursor/skills` and let the result decide.
   - **Either path exists** — including an *empty* one, which means the feature is present and unused — treat it as a native skills directory and let DP-1 route normally (A or B). Adding the rules line as well is cheap insurance and worth doing.
   - **Neither exists** — take option C and use the rules system (`.cursor/rules/*.mdc`). C works fully; nothing downstream is degraded by it.
   Don't create a skills directory that wasn't there to make option A available — a directory you invented is one nothing reads.
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

**A recipe (do it in Phase 2):** create `<workspace>/skills/`, then for each skill `ln -s "<absolute path to the skill folder>" "<discovery dir>/<skill-name>"`. Absolute paths only — a relative symlink target resolves against the link's directory, not yours.

**Verifying the install — the check differs per branch, and only A uses `ls -lL`:**

| DP-1 | Verify with | Why |
|---|---|---|
| **A** | `ls -lL "<discovery dir>/<skill-name>/SKILL.md"` | The `-L` follows the link, so a wrong target errors. Plain `ls -l` prints a healthy-looking line for a *broken* link and exits 0 — that is the trap this catches. |
| **B** | `ls "<discovery dir>/<skill-name>/SKILL.md"` | No link involved; existence is the whole check. |
| **C** | `test -f "<path>/SKILL.md" && echo ok` **and** `grep -n "<path>" <rules file>` | Two halves: the skill exists, *and* the routing line actually names its path. A skill nothing points at is invisible; a routing line pointing at nothing is worse. |
| **D** | The user opens the folder and finds the skill from the index. | Nothing to resolve. Say that's the check you ran rather than reporting a verification you didn't do. |

### DP-2 — Do skill folders take an ordering prefix?

**Options:**
- **A. Bare names** — `outreach-drafter/`. *Trade-off:* a folder of thirty skills sorts alphabetically and tells you nothing about what came when.
- **B. Numbered prefix on the storage folder** — `02 outreach-drafter/`, with the frontmatter `name` and the discovery-directory entry both staying bare. *Trade-off:* you must check the highest number before each build, and the number is meaningless the moment you'd want to reorder (you never renumber — links point at the folder).

**Recommendation:** **B only when DP-1 = A.** That is the one configuration where numbering is free: the storage folder carries the number, the bare-named symlink is what the platform sees, and the format's name-matches-folder rule still holds at the point of discovery. It's the kit originator's setup, and the numbers are creation order, so the folder doubles as a build log.

**A for every other DP-1 option** — under B the folder *is* the discovery entry, and under C and D the folder name is the only name the skill ever has, so a prefix breaks the name-matches-folder rule everywhere and quietly blocks a later move to a real skills directory. Under C and D you already have an ordered list of every skill: the index table. Use that and keep the folders bare. Default if the user shrugs: B when DP-1 is A, A otherwise.

**The rule that makes B safe:** the number lives on the *storage* folder only. The frontmatter `name` is always bare, and the symlink in the discovery directory is always bare — so the directory the platform actually reads is named exactly the skill's `name`. Put a numbered folder anywhere a platform reads directly and that rule breaks: the skill may never load, and a spec validator will reject it. `skill-creation/references/skill-anatomy.md` states this; if the user picks A, that section applies to nothing and does no harm.

### DP-3 — Does a complex skill get a companion project folder?

A companion folder (`README` + `STATUS` + `DESIGN`, and for the biggest builds `PLAN` + `sessions/` + `artifacts/`) sits outside the skill and holds the *build* record: why it exists, what was decided, what's left. The skill folder holds the procedure; the project folder holds its history.

**Options:**
- **A. Never** — the skill folder is the whole artefact. *Trade-off:* six months later nobody remembers why a rule in the skill exists, so it gets "cleaned up" and something breaks.
- **B. Tier 3 only** — only skills with scripts, APIs or file output get the folder. *Trade-off:* a heavily-iterated Tier 2 skill (a voice-matching one, typically) has nowhere to record what was tuned.
- **C. Tier 2 and 3** — the kit originator's setup. *Trade-off:* two folders per skill, and if nobody reads `STATUS.md` they're just clutter.

**Recommendation:** **C if the user already keeps project folders with status files** — the habit exists, the folder gets read, and voice-tuned skills are exactly the ones whose decisions get forgotten. **B if they don't** — start where the payoff is unambiguous, and add Tier 2 later if they find themselves wanting it. **A only if the user is solo, building a handful of simple skills, and says outright they won't read the folder** — an unread status file is worse than none, because it looks authoritative while going stale. Default: **C if the scan found at least one project folder containing a state file** — a `STATUS.md`, `PROGRESS.md`, a running log, anything that gets *updated* rather than written once. A `README.md` on its own doesn't count: it proves they document, not that they maintain. **Otherwise B.** If the two signals disagree — a team that keeps no state files, or a solo operator who keeps meticulous ones — **the state-file habit wins**, because it predicts whether the folder gets read; team size only predicts whether it *should* be. Record the parent path; the default is `projects/<skill-name>/` in the workspace.

### DP-4 — How does the dry-run gate run?

Every skill gets tested twice: a **dry-run** (someone follows the new `SKILL.md` cold, before the user ever relies on it) and an **acceptance** run in a fresh session. The acceptance run is always the user's. The dry-run is the one with options.

**Options:**
- **A. A fresh subagent** — you launch one, hand it the payload below, and read its output. *Trade-off:* needs a platform that runs subagents. Note the scope: a subagent can *dry-run a finished skill*, which is all this gate asks. It cannot run the build funnel itself, because the spec block requires the user's confirmation and a subagent has no user — never delegate a whole skill build to one.
- **B. A fresh session the user drives** — the user opens a new chat or composer, pastes a prepared payload, and brings the result back. *Trade-off:* a real stop; you wait for them. It is also the *better* test — a genuinely separate context, not a child of yours. **What makes it cold is the empty context, not a second person:** a solo operator opening a new composer gets a real cold read, as long as only the payload goes into it. Pasting the build conversation alongside turns B into C without anyone noticing.
  **Don't ask the user to assemble the payload — write it for them.** Save a file (`_dryrun-<skill-name>.md`, kept *outside* any directory the platform scans for skills) containing the new `SKILL.md`, every `references/` file it points at, any standing-instruction rules it points at, any workspace file the skill reads, and one approved prompt at the bottom. Hand over the path and say: "Open a new session, paste this whole file, and send me back what it produces — don't add anything else." Delete it once the gate passes.
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

**If DP-1 = C (a routing line rather than a skills directory), this is option A in a different costume — record it as "A, via routing rule".** The routing line in the rules file plays the part the description plays elsewhere: the AI reads it every session and matches the user's request against the triggers it names. So the trigger phrases matter *twice* — once in the skill's `description`, once in the routing line — and they should be the same phrases. Adding B on top (a memorable name the user can invoke outright) is cheap and worth it.

Whichever you pick: the **description discipline in `skill-creation/SKILL.md` Step 6b applies anyway.** Under-triggering is the normal failure mode — descriptions get written as summaries of what the skill does, when they need to be lists of when to use it. And on any platform that loads skills at session start, tell the user now: a skill just installed will not fire in *this* session. That's not a bug and it's why acceptance testing happens in a fresh one.

### DP-6 — How much interview before the build?

**Options:**
- **A. One question at a time** — ask, wait, ask the next. *Trade-off:* six round-trips before anything gets written.
- **B. All questions in one block** — the user answers in a single pass. *Trade-off:* a wall of questions gets skimmed, and skimmed answers are how a skill ends up with a `references/` folder nobody needed.
- **C. Infer and confirm** — draft the spec block straight from the user's opening description, present it, let them correct it. *Trade-off:* if the description was thin, the spec is a guess and the user is now editing your assumptions instead of stating their own.

**Recommendation:** **A when the request arrives as one line or a vague gesture** ("something for the weekly report thing") — the originator's default, because the answers are the build and shallow answers make a shallow skill. **C when the user's opening message already answers most of Q1–Q6** — the skill's Step 1 and Step 4 both say to skip what you already know, so a detailed brief should not be re-interrogated. B is the worst of both and isn't recommended. Default: A if the trigger message is under about three sentences, C if it's longer and already covers task, inputs and output. Either way the spec block is the real gate — it is never skipped, whichever mode ran.

## Phase 2 — Install

1. **Install the skill** at the DP-1 location. The folder is named `skill-creation` — plus the DP-2 prefix on the *storage* folder if prefixes are on, never on whatever the platform reads.
   - **DP-1 = A:** copy `skill-creation/` to `<workspace>/skills/skill-creation/` (with the DP-2 prefix on the folder if prefixes are on), then symlink it into the discovery directory under the bare name and verify with `ls -lL`.
   - **DP-1 = B:** copy `skill-creation/` straight into the discovery directory as `skill-creation/`.
   - **DP-1 = C:** copy `skill-creation/` to `<workspace>/skills/skill-creation/`; the routing happens in step 4.
   - **DP-1 = D:** the user keeps `skill-creation/SKILL.md` and its three references as the documents they paste.
   - **Resolve the placeholders in the installed copy.** `skill-creation/SKILL.md` ships with `<skills>` and `<projects>` written as placeholders; in the copy you install, replace every one with the real paths chosen above. Leaving them is how a future session ends up reading "the install record at `<skills>/README.md`" and having to guess what `<skills>` means — the file that answers the question is the one it can't find. Grep the installed copy for `<` afterwards; only the illustrative `<name>` and `<triggers>` examples in the references should survive.
   - Adapt minimally otherwise. Only rename terms that clash with the user's existing vocabulary — if they call them "playbooks", say playbooks. Don't rewrite the procedure.
   - **No companion project folder for `skill-creation` itself**, whatever DP-3 says. It arrives finished and tested; the folder exists to record a build you did, and you didn't build this one. DP-3 starts applying at the first skill you build in Phase 3.
2. **Create the skills index:** `<skills>/README.md` from `templates/SKILLS-INDEX.md`. Fill it in **with the user** — the library table seeded with `skill-creation` itself, the conventions section matching what they actually chose, and the **Install record** completed with all six decisions. Never install it with placeholders left in; the record is how a session six months from now knows how skills get built here.
3. **Install the skill template** (optional, default yes): `templates/SKILL-TEMPLATE.md` → `<skills>/SKILL-TEMPLATE.md`, as a loose file. **Do not install it as a `SKILL.md` inside a folder**, and never inside a discovery directory: its frontmatter is placeholders (`name: SKILL_NAME`), which is not spec-valid, so a platform scanning that directory would either reject it or register a broken skill. Ask before installing it; some users would rather the skill write each one from scratch.
4. **Wire the standing instructions file.** This is the stickiness step — an install that only creates folders dies at the user's first new chat.

   **Merge, or a new file?** If the platform uses a single instructions *file* (`CLAUDE.md`, `AGENTS.md`, `GEMINI.md`), append to it in the user's own format — read it first, match its headings, never rewrite what's there. If the platform uses a rules *directory* (`.cursor/rules/*.mdc`), **a new scoped file is the merge** — that is how the directory is designed to work, and editing their existing rule file risks clobbering rules you don't understand. Either way, read what already exists first: if a rule already covers skill routing, extend it instead of adding a competing one. Then add:

   > **Building skills.** Before building anything new, check `<skills>/README.md` — a skill may already cover it. When the user wants to automate a repeating workflow, turn a prompt into something reusable, or says "build me a skill", use the `skill-creation` skill: it gates whether the thing should be a skill at all, then scaffolds, installs and tests it. Every new skill gets registered in that index.

   For **DP-1 = C**, add the per-skill routing line the same place: "When the user asks about \<triggers\>, read `<path>/SKILL.md` and follow it."

   **Under DP-1 = C you will end up naming the skill twice** — once in the generic block above, once in its per-skill routing line — and that is correct, not a duplicate to clean up. So expect more than one `grep` hit when you verify; you are checking that *at least one* line names the path, not that exactly one does.

   **What counts as merging:** appending a section in the file's own style, and adding an entry to a list that already exists (a "where things live" list gaining a `skills/` row is a merge, not a rewrite). What is never merging: editing or reordering lines that were already there for other reasons. If you're unsure whether an edit crosses that line, show the user the diff instead of asking permission in the abstract.

   **Scope warning:** some platforms scope instruction files to a folder or workspace rather than the user globally — Cursor uses `.cursor/rules/*.mdc` (create `.cursor/rules/skills.mdc` with frontmatter `description: Skill library routing` and `alwaysApply: true`; without `alwaysApply` the rule may never fire), Codex-style agents use `AGENTS.md` in the repo root, Copilot uses `.github/copilot-instructions.md`. Put the wiring where the user's AI actually works, and **tell them which file it landed in.** If the user works across several repos, say plainly that the line needs repeating in each, or that it belongs in their global instructions file if the platform has one.
5. **Record every path installed** in `STATUS.md`.

## Phase 3 — First live build — on something real

Build **one real skill the user actually wants**, not a toy. This is the phase that proves the install and teaches the funnel at the same time.

1. **Pick it with the user.** Start with what the Phase 0 scan already found: if there's a `prompts/`, `snippets/` or `commands/` folder, read it out and ask which of those they still use — a stale saved prompt someone keeps re-editing is the single best first candidate, and running it through the Step 0 gate shows the user immediately which of their saved prompts should have been something else. If there's nothing there, ask: "What do you explain to your AI more than once a month?" Steer toward something small — a Tier 1 or Tier 2 with one reference file. A first build that needs API credentials will stall on the credentials and teach nothing about the funnel.
2. **Run `skill-creation/SKILL.md` end to end, out loud:** the Step 0 gate (say the verdict, don't just think it) → tier set silently → interview per DP-6 → **spec block, confirmed** → write the folder → install and verify discovery → generate realistic test prompts → **dry-run per DP-4, all four checklist items reported, including the value check** (on DP-4 = B, write the paste payload to a file and hand over the path — don't make the user assemble it) → register in `<skills>/README.md`.
   **Retire what the new skill replaced.** If the candidate came from a saved prompt, delete that prompt once the skill passes its acceptance test — or replace its contents with one line pointing at the skill. Leaving both means the next person to reach for the prompt gets the stale version, and the skill silently stops being the source of truth.
   **If the Step 0 gate says no** — which happens, and is a success, not a failure — say which container it should be instead, build *that* (a line in the instructions file, a script, a saved prompt), then pick a different candidate and run the funnel again. The user needs to see the funnel complete once.
3. **Verification test — the acceptance gate (Step 8b), and a real stop.** The user opens a **fresh session**, types one of the approved test prompts the way they'd say it on a workday, and reports back two things: did the skill get picked up, and was the output right? Tell them exactly what to type and then wait — Phase 4 does not start until the answer is back. Record the prompt and a one-line summary of the result in `STATUS.md`.
   - It fired and the output was right → the install works.
   - It didn't fire (DP-5 = A or C) → the description is the problem, not the skill. Rewrite it per Step 6b — more trigger phrases, more of the user's actual words, no workflow summary — reinstall, and have them try again in another fresh session.
   - It fired but the output was wrong → the body is the problem. Fix the step that went wrong and re-run the dry-run gate only.
   - The platform has no automatic invocation (DP-5 = B or D) → the test is that the user can find the skill in the index and invoke it by name, and that it runs correctly cold.

## Phase 4 — Wrap

1. Walk the user through what's installed and where: the skill, the skills index, the instructions-file wiring (name the file), and the first skill they built.
2. Tick every phase in `STATUS.md`; empty the Blockers row or state what remains.
3. **Confirm the install record in `<skills>/README.md` is filled in** — every row, including the verification command and the subagent/model-choice row. That block plus the copied `STATUS.md` are the only things that survive this session, and every future build reads them.
4. Leave the habit behind, one line: **"The third time you explain the same thing to your AI, that's a skill — and check the index before building, because it might already be one."**
5. **Give `STATUS.md` a home before anything else.** It is the resume spine, it lives in this repo, and the next step says the repo is disposable — so copy it to `<skills>/install-record-STATUS.md` (or fold its decisions table into the index's Install record and say you've done so). On a platform with no persistent memory this file is the only thing that remembers how the install went; losing it means the next session re-derives your choices by guessing.
6. Only then is the kit repo safe to delete. Keeping it for re-reads is also fine — nothing downstream depends on it.

## If things go wrong

- **A capability is missing** (no subagents, no shell, no skills directory, no file access): take the nearest degradation path named in the DP. Every step has a sequential, routed or paste-in form — nothing here dead-ends.
- **The user's conventions clash with the kit's names:** keep their names, map the jobs. The skills folder can be called anything; `references/` can be `docs/` if that's their habit. Two things are not negotiable because the format depends on them: the file is `SKILL.md`, and the folder name matches the frontmatter `name` at the point of discovery.
- **The skill doesn't fire in a fresh session:** in order — is the discovery path right (`ls -lL` through the link)? Does the folder name equal the `name`? Is the description written as *when to use it* rather than *what it does*? That third one is the answer more often than the first two combined.
- **The user wants a skill for something that failed the Step 0 gate:** build the right container instead and say why. If they insist, build the skill and note in the index that it was built over the gate — a library where the gate never says no is a library of folders nobody uses.
- **A step fails twice:** don't loop. Note it in `STATUS.md` Blockers, take the fallback, and tell the user.
