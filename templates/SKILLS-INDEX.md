# Skills

Every skill built here, and the rules for building the next one.

**Before building anything new:** scan the table below. If a skill already covers the job, use or extend it — two skills doing overlapping work is how a library rots. If nothing covers it, run the `skill-creation` skill; it gates whether the thing should be a skill at all before it scaffolds anything.

## The library

| Skill | What it does | Tier | Say this to fire it |
|---|---|---|---|
| `skill-creation` | Builds a new skill end to end: gate, size, interview, spec, write, install, test | 3 | "build me a skill for…" |

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
| **Platform** | WHICH AI TOOL — it decides the shape of a routing line (a `.mdc` rule with `alwaysApply: true`, versus a plain line in an `AGENTS.md`-style file) and which paths exist at all |
| Discovery method (DP-1) | **A** canonical in the workspace, symlinked into the discovery directory / **B** canonical in the platform's discovery directory / **C** canonical in the workspace, routed by an instructions line / **D** documents the user pastes |
| **Discovery directory** | THE ACTUAL PATH the platform scans (e.g. `~/.claude/skills/`, `.claude/skills/`) — or, on **C**/**D**, `none — routed by <instructions file>` / `none — paste-in`. Every future install writes here; don't leave it to be reverse-engineered out of the verification command below. |
| **Link or copy** | symlinked (edits are live) / copied (edits need re-copying — say so, and re-copy after every skill edit) / neither, the folder is read in place |
| **Discovery verified by** | THE ACTUAL COMMAND for this setup — e.g. `ls -lL <dir>/<name>/SKILL.md`, or `test -f <path>/SKILL.md` plus `grep -n "<path>" <instructions file>`. Write the command, not the word "verified": every future build re-runs it. |
| Ordering prefixes on folders (DP-2) | yes — two digits, zero-padded, space-separated, starting at `01`; **derive the next number by listing the folder, never from a written-down counter** / no |
| Companion project folder (DP-3) | never / Tier 3 only / Tier 2 and 3 — at PATH |
| Dry-run gate (DP-4) | fresh subagent / fresh session the user runs / self-review |
| Invocation (DP-5) | automatic from the description / routing line ("A via routing rule") / explicit command / both / paste-in |
| Interview mode (DP-6) | the rule, not one word — one question at a time / batch (all at once) / infer-and-confirm. E.g. "one question at a time by default, dropping to infer-and-confirm when the request already covers task, inputs and output" |
| **Subagents / per-task model choice** | yes / no — a skill's mechanical steps are only marked for cheap-model dispatch where this is yes |
| Instructions file wired | PATH (the file holding the line that routes skill requests here) |
| Install transcript | `install-record-STATUS.md` in this folder, if it was copied here — a **frozen** record of how the original install went: scan findings, decisions, the first skill built. Read it for context on *why* a choice was made; never update it, and never treat it as current state. This table is the live record. Delete the row if no such file was kept. |

## Teammate setup

*Only if this library is shared.* Each person needs two things:

1. Discovery — whatever the record above names, pointing at **their** local path.
2. The routing line in their standing instructions file.

**Whether these travel with a clone depends on the discovery method.** If discovery is a routing line in an instructions file that is *committed to the shared repo* (`AGENTS.md`, `.cursor/rules/`), both halves clone with it and a teammate needs to do nothing — check that before sending anyone setup steps. If discovery is a symlink into a personal directory (`~/.claude/skills/`), or the instructions file lives outside the repo, neither travels and each person must do both by hand, pointing at their own absolute paths.

If a teammate says "the skill never fires," it is almost always one of those two — or an absolute path in a committed file that only resolves on the machine that wrote it.
