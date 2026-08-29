# Skill anatomy — the portable format

A skill is a folder with a `SKILL.md` at its root. The format is an open specification (agentskills.io) supported by a long and growing list of agents, so a skill written to it moves between tools without a rewrite. Write to the spec even if your platform is lenient — the day you switch tools, or hand a skill to a teammate on a different one, the strictness pays for itself.

## The folder

```
skill-name/
├── SKILL.md        ← required: frontmatter + instructions
├── references/     ← optional: knowledge files loaded on demand
├── scripts/        ← optional: executable code
└── assets/         ← optional: templates, data, images
```

**The folder name must equal the frontmatter `name`.** This is a spec rule, not a convention, and it is the one that quietly breaks installs. See "Ordering prefixes" below if your install record says folders take a number.

## Frontmatter

Required:

| Field | Rules |
|---|---|
| `name` | 1–64 characters. Lowercase letters, digits and hyphens only. No leading or trailing hyphen, no doubled hyphens. Must match the folder name. Some platforms additionally reject vendor words (e.g. "claude", "anthropic") and XML tags in the name — avoid both and you're portable everywhere. |
| `description` | 1–1024 characters, non-empty, no XML angle brackets. Must convey **what** the skill does and **when** to use it. This is the only part of the skill loaded into every session, so it is the entire basis on which the skill does or doesn't fire. |

Optional, all rarely needed:

| Field | What it's for |
|---|---|
| `license` | A license name (`MIT`, `Apache-2.0`) or a pointer to a bundled licence file. Worth setting on anything you'll share. |
| `compatibility` | Up to 500 characters describing environment requirements — a required tool, a system package, network access. Most skills need none. Use it rather than burying a hard requirement in the body. |
| `metadata` | A free-form map of string keys to string values for client-specific extras — `author`, `version`. |
| `allowed-tools` | A space-separated list of pre-approved tools (e.g. `Read Bash(git:*)`). Marked experimental in the spec; support varies. Don't rely on it. |

Minimal valid skill:

```markdown
---
name: skill-name
description: A description of what this skill does and when to use it.
---

The instructions.
```

## Scripts: paths and working directory

A `scripts/` file is the one part of a skill that breaks silently on the wrong platform, and the cause is almost always the working directory. **The session's working directory is wherever the user started it — usually the workspace root, not the skill's folder.** So a skill that says `python3 scripts/estimate.py` works when you test it from inside the skill folder and fails with "No such file or directory" for every real run.

Write script invocations so they don't care where the session sits:

- **Reference the script from the skill folder, not the working directory.** In `SKILL.md`, write the path as it is from the skill's own root and say so explicitly: "run `scripts/estimate.py` — the path is relative to this skill's folder, so resolve it against wherever this `SKILL.md` lives." An agent that knows where it read the file from can resolve that; one given a bare relative path cannot.
- **On a routed install** (the skill lives in the workspace and an instructions line points at it), the safest form is the full workspace-relative path: `python3 skills/<skill-name>/scripts/estimate.py`. It is uglier and it always works.
- **State the runtime.** "Run with `python3` (3.9+)" or "run with `node`". Never leave the language to be inferred from the extension — the interpreter may not exist, and the failure appears at the worst moment.
- **Test the invocation from the workspace root before shipping**, not from the skill folder. That is where it will actually run.

## Size and layering

The format assumes **progressive disclosure** in three stages, and the sizes follow from it:

1. **Frontmatter** — always loaded, in every session. Roughly 100 tokens. Keep it tight; this is rent you pay constantly.
2. **`SKILL.md` body** — loaded when the skill fires. Target under ~5,000 tokens and hard-cap at 500 lines. Past that, the body is carrying reference material that should be a file.
3. **`references/`, `scripts/`, `assets/`** — loaded only when the workflow actually reaches them. Free until used.

**Reference depth rule:** reference files sit one level below `SKILL.md` and are pointed to by relative path. Never a reference that points to a reference that points to a reference — weaker agents lose the thread, and every hop is a chance to load the wrong thing.

## Ordering prefixes

Some people number their skill folders (`01 note-taker`, `02 outreach-drafter`) so the directory reads as a list in creation order. If the install record says prefixes are on, the rule is exact:

- The **number lives on the storage folder only.**
- The frontmatter `name` is always bare: `outreach-drafter`, never `02 outreach-drafter`.
- The entry in the platform's **discovery directory** is always bare too — a symlink or copy named `outreach-drafter`.

That last point is what keeps a numbered folder spec-compliant: at the point of discovery the parent directory *is* named exactly the skill's name. Put a numbered folder directly into a discovery directory and the name/folder rule breaks — the platform may not find it, and a spec validator will reject it.

**The format:** two digits, zero-padded, then a single space, then the bare name — `01 note-taker`, `02 outreach-drafter`. The first skill is `01`. Past 99, go to three digits for new folders and leave the old ones alone.

**Finding the next number:** list the storage folder and add one to the highest you see. If the folder is empty, the next number is `01`. **Derive it by listing, every time** — don't trust a written-down counter, which goes stale the moment a skill is added by hand. Never renumber an existing folder: links and habits point at it.

A space in the folder name means every shell command touching it must quote the path (`ln -s "…/02 outreach-drafter" …`). That is the cost of the convention; if it bothers you, use `02-outreach-drafter` instead and keep it consistent.

## Discovery — getting the platform to see it

The install record names the choice. The mechanics per platform family:

| Platform | Where it looks | Verify |
|---|---|---|
| Claude Code | `~/.claude/skills/<name>/` (personal) or `.claude/skills/<name>/` (project) | `ls -lL ~/.claude/skills/<name>/SKILL.md` |
| Any agent with a native skills directory | that directory, one folder per skill, folder named `<name>` | list the file through the link, as above |
| Cursor | Skills-directory support is version-dependent — probe `~/.cursor/skills` and `.cursor/skills` rather than assuming either way. **The mechanism that works on every version is the rules directory:** keep the skill folder in the workspace and route to it from a `.mdc` rule with frontmatter `description:` and `alwaysApply: true` (without `alwaysApply` the rule may never fire), whose body says: "When the user asks about \<triggers\>, read `<path>/SKILL.md` and follow it." **Add it to the routing file your install record names — one shared file (`.cursor/rules/skills.mdc`) holding a line per skill is the default**; only split to one rule file per skill if the record says so, or the routing fragments and the recorded verification command stops matching. Note `.cursor/rules/` is workspace-scoped: it needs repeating per workspace. | `test -f "<path>/SKILL.md"` **and** `grep -n "<path>" <the routing file your install record names>` — the file exists *and* something points at it |
| Codex / Gemini CLI / Copilot / any AGENTS.md-style agent | no skills directory: keep the folder in the workspace and add the same routing line to `AGENTS.md` / `GEMINI.md` / `.github/copilot-instructions.md` | `test -f "<path>/SKILL.md"` **and** `grep -n "<path>" AGENTS.md` — the skill exists *and* something routes to it |
| Chat-only | no discovery at all: the skill is a document the user pastes. Keep an index of them so the user can find the right one. | paste it once and run a trigger prompt |

**The keep-one-copy rule.** If the skill's canonical home is the user's workspace and the platform reads from somewhere else, **link, don't copy** — a symlink from the discovery directory to the workspace folder means edits are live and there is exactly one version:

```bash
ln -s "/absolute/path/to/workspace/skills/02 outreach-drafter" ~/.claude/skills/outreach-drafter
ls -lL ~/.claude/skills/outreach-drafter/SKILL.md   # dereferences: fails loudly if the target path is wrong
```

**If the discovery path is already taken** — `<discovery dir>/<name>` exists as a real directory rather than your link — stop and look before doing anything. If it's an older copy of the same skill, back it up (`mv` it aside with a dated suffix) and tell the user where it went. If it's a *different* skill that happens to share the name, the name is the problem: rename yours and update the frontmatter to match. Never delete a directory at a discovery path to make room — you cannot tell from the path alone whether it is disposable, and skills are exactly the thing people forget they installed.

`ls -lL` is the verification, not `ls -l`: the `-L` follows the link, so a broken target errors, where plain `ls -l` prints a healthy-looking line for a broken link and exits 0. **`-L` is only meaningful when there is a link** — on a direct install just check the file exists, and on a routed install check both that the file exists and that the routing line names its path. Report which check you ran; "verified" without naming the check is how a bad install gets signed off. If your platform or filesystem doesn't follow symlinks, copy instead and note in the install record that edits need re-copying — a stale copy is the second most common way a skill misbehaves after a broken link.

**Install check versus acceptance test — don't confuse them.** Everything in the table above is an *install* check: it proves the files are where they should be and something points at them. It runs immediately. Whether the skill actually *fires* is a separate question, answered only by starting a fresh session and using a real trigger phrase — see `SKILL.md` Step 8b. A passing install check on a skill that never fires means the description or the routing line is wrong, not the install.

## When to validate

The spec ships a reference validator (`skills-ref`, in the agentskills reference library on GitHub) that checks frontmatter validity and naming — roughly `skills-ref validate ./my-skill`. Check that repository for current install instructions before relying on the exact invocation. It's a nice-to-have when publishing a skill or handing one to a teammate on a different tool — **not a step to block on.** For a personal skill, the checks in this file are the same ones it makes, and running them by eye is enough. If the tool isn't already installed, skip it rather than going down an install rabbit hole mid-build.
