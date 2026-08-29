# templates/ — install matrix

> **This file is install-time documentation.** It maps templates to the decisions that gate them, for whoever is running the original install. It is not installed into the workspace and nothing later depends on it — if you are reading this after the install, you can ignore it.

Files the installer creates in the user's workspace during the install walkthrough, and which decision gates each.

| Template | Installed as | Gated by | Notes |
|---|---|---|---|
| `SKILLS-INDEX.md` | `<skills>/README.md` | DP-1 (skills folder location + discovery); the Install record block summarises DP-1…DP-6 | The shelf index every new skill is registered in, and the memory of how skills get built here. Filled in *with the user* — never installed with placeholders left in. |
| `SKILL-TEMPLATE.md` | `<skills>/SKILL-TEMPLATE.md`, as a **loose file** — never as a `SKILL.md` inside a folder, and never inside a discovery directory (its placeholder frontmatter isn't spec-valid, so a scanner would reject it or register a broken skill) | DP-1 (where it lives). Its edge-cases section is used only for Tier 2+; its scripts pointer only when Q4 = yes | The skeleton a new `SKILL.md` starts from. Optional to install: the `skill-creation` skill can write one from scratch, but a template in the workspace keeps house style consistent when a human writes one by hand. Ask the user; default is to install it. |

The skill itself (`skill-creation/SKILL.md` + three references) is not a template — it installs verbatim, adapted only where the install record's choices change wording.
