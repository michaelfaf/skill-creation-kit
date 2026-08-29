# Skill Creation — implementation kit

A funnel for turning a workflow you keep explaining to your AI into a **skill**: a folder with a written procedure your AI finds and follows on its own. It gates the idea first, sizes it, interviews you for what it needs, confirms a spec before writing anything, installs it where your platform actually looks, and tests it twice. Extracted from a working system in daily use. This repo isn't the skills — it's an installer that adapts the funnel to *your* stack and builds your first skill with you.

## Read this first — what this is and why

**The problem.** You explain the same thing to your AI over and over — your update format, how you want transcripts turned into notes, the checks you run before something goes out. Each time you explain it differently, so you get different output, and the version you liked lives in a chat you can't find. Saving the prompt works until the task has an edge case in it. And the opposite failure is just as common: people discover skills, build fifteen folders in a weekend, and twelve of them should have been one line of instructions or a small script. Those never fire, and they bury the three that work.

**What this installs.** A skill that builds skills. It **gates the request first** — most requests should be a line in your instructions file, a script, or just doing the thing once, and it says so — then sizes what's left, asks six questions (each one deciding whether the skill needs a knowledge file, a script, or neither), confirms a spec block before writing anything, installs the skill where your platform discovers it, **verifies it actually resolves**, and tests it twice: once cold by someone with no context, once by you in a fresh session, because most platforms can't test a skill they just installed. Then it registers it in an index so you stop rebuilding what you already have.

**Use it if:** you've explained the same workflow to your AI more than twice · you've built skills that never fire and don't know why · you want your team building skills the same way instead of six different ways · you suspect half your saved prompts should be something else · you're about to build your first one and would rather not learn the failure modes the slow way.

**Concrete use cases:** turn the weekly update you hate writing into a skill that drafts it in your voice · package the checks you run before a proposal goes out · capture the way *you* want call notes structured, once · hand a teammate the same funnel so their skills work like yours.

**It is not** a prompt library, an agent framework, or a way to make your AI smarter in general. It makes it reliable at the things you do repeatedly — and it will tell you when the thing you're asking for isn't one of them.

**→ Want the full picture first?** [OVERVIEW.md](OVERVIEW.md) — every concept, how a build flows, what gets installed, your role versus your AI's. Then [EXAMPLE-SKILL.md](EXAMPLE-SKILL.md) — one real skill from request to shipped, plus a request that correctly failed the gate. Ten minutes for both.

## How to use it — three on-ramps

1. **You have a coding agent** (Claude Code, Cursor, Codex, Copilot, Amp, Gemini CLI…): open this repo with it and say **"Read IMPLEMENT.md and walk me through it."** It scans your setup, presents six decisions, installs the skill, and builds one real skill with you.
2. **You have a chat-only AI:** paste `IMPLEMENT.md` into the chat, follow along, create the files yourself, and keep `STATUS.md` as a note you paste back each session. Skills become documents you paste — the funnel is unchanged.
3. **No AI at all:** read `IMPLEMENT.md` yourself. Every step is doable by hand, and a skill is just a markdown file in a folder.

**What you'll need:** almost nothing. File access and a text editor is the floor. A shell makes the install cleaner; a platform with a native skills directory makes discovery automatic; subagents make the dry-run instant. Each of those absences is an explicit option in the installer, not a dead end.

**Model recommendation:** run the install on the most capable model you have, at high reasoning effort (Claude Fable 5 at high effort if available, otherwise Opus at high; on other platforms, their strongest setting). The judgment calls — is this really a skill, is this description going to fire, is this skill earning its load — are exactly the ones a cheap model gets confidently wrong.

## What's in here

| Path | What it is |
|---|---|
| `OVERVIEW.md` | The full explanation — read this to understand the system before installing |
| `EXAMPLE-SKILL.md` | The worked example: one skill from request to shipped, one request that failed the gate, and how other skill shapes differ |
| `IMPLEMENT.md` | The installer script, written to your AI (you can follow it too) |
| `STATUS.md` | Install progress — scan results, decisions, phase ticks; the resume spine if the session dies |
| `AGENTS.md` / `CLAUDE.md` | Entry instructions for coding agents that auto-read those files |
| `skill/SKILL.md` | The skill itself, in portable agent-skill format |
| `skill/references/tier-routing.md` | How a skill gets sized, and the brainstorming fallback when no brainstorming skill exists |
| `skill/references/dbs-questions.md` | The interview — six base questions, four more for complex skills |
| `skill/references/skill-anatomy.md` | The portable format: frontmatter fields and limits, folder rules, per-platform discovery paths, how to verify an install resolves |
| `templates/SKILLS-INDEX.md` | The skills index the installer creates for you, with the install record |
| `templates/SKILL-TEMPLATE.md` | A `SKILL.md` skeleton for writing one by hand — see `templates/README.md` for what maps to which decision |

## What you end up with

- The **skill-creation skill** installed where your AI finds it — and verified to actually resolve, not just copied.
- A **skills folder with an index**: every skill listed with its trigger phrase, plus a record of the six choices you made, so future builds match this one.
- A line in your AI's **standing instructions** so "build me a skill for…" routes here automatically instead of getting improvised.
- **One real skill of your own**, built during the install and tested in a fresh session.
- A habit worth more than any of it: **the third time you explain the same thing to your AI, that's a skill — and check the index first, because it might already be one.**
