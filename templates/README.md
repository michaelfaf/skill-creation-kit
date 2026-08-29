# templates/ — install matrix

Files the installer (IMPLEMENT.md Phase 2) creates in the user's workspace, and which decision gates each.

| Template | Installed as | Gated by | Notes |
|---|---|---|---|
| `SKILLS-INDEX.md` | `<skills>/README.md` | DP-1 (skills folder location + discovery); the Install record block summarises DP-1…DP-6 | The shelf index every new skill is registered in, and the memory of how skills get built here. Filled in *with the user* — never installed with placeholders left in. |
| `SKILL-TEMPLATE.md` | `<skills>/_template/SKILL.md` — or kept in the kit and copied per build | DP-1 (where it lives). Its edge-cases section is used only for Tier 2+; its scripts pointer only when Q4 = yes | The skeleton a new `SKILL.md` starts from. Optional to install: the `skill-creation` skill can write a `SKILL.md` from scratch, but a template in the workspace keeps house style consistent when a human writes one by hand. Ask the user; default is to install it. |

The skill itself (`skill/SKILL.md` + three references) is not a template — it installs verbatim, adapted only where the install record's choices change wording.
