# Skills

Every skill built here, and the rules for building the next one.

**Before building anything new:** scan the table below. If a skill already covers the job, use or extend it — two skills doing overlapping work is how a library rots. If nothing covers it, run the `skill-creation` skill; it gates whether the thing should be a skill at all before it scaffolds anything.

## The library

| Skill | What it does | Tier | Say this to fire it |
|---|---|---|---|
| `skill-creation` | Builds a new skill end to end: gate, size, interview, spec, write, install, test | 3 | "build me a skill for…" |
| | | | |

<!-- Add one row per skill at ship time (skill-creation Step 9). A skill that isn't in
     this table is a skill nobody knows exists, including a future session of your own AI. -->

## Conventions

- **Structure:** every skill is a folder with `SKILL.md` at its root, plus `references/` (knowledge) and `scripts/` (code) only when it needs them.
- **Naming:** lowercase letters, digits and hyphens. The frontmatter `name` always equals the folder name at the point the AI discovers it.
- **Size:** `SKILL.md` under 500 lines. Longer means reference material belongs in `references/`.
- **Don't copy knowledge:** if a fact, a voice guide or a format already lives somewhere in this workspace, point at it from the skill — don't paste a second copy that will drift.
- **Retire, don't accumulate:** a skill that hasn't fired in months either has a bad description or shouldn't exist. Fix the description or delete it.

## Install record

*Written once at install. Later sessions read this to know how skills get built here — keep it current if a choice changes.*

| Setting | Choice |
|---|---|
| Skills folder | PATH |
| Discovery method | HOW THE AI FINDS SKILLS — native skills directory / symlink from workspace / routing line in the instructions file / paste-in |
| Ordering prefixes on folders | yes (next number: N) / no |
| Companion project folder | never / Tier 3 only / Tier 2 and 3 — at PATH |
| Dry-run gate | fresh subagent / fresh session the user runs / self-review |
| Invocation | automatic from the description / explicit command / both / paste-in |
| Interview mode | one question at a time / batch / infer-and-confirm |
| Instructions file wired | PATH (the line that routes skill requests here) |

## Teammate setup

*Only if this library is shared.* Each person needs two things that live outside this folder:

1. Discovery — whatever the record above names, pointing at **their** local path.
2. The routing line in **their** standing instructions file.

Neither travels with a clone. If a teammate says "the skill never fires," it's almost always one of these two.
