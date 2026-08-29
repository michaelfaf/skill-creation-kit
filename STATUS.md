# Kit progress

> Maintained by the implementing AI. Tick tasks as they complete; record decisions the moment they're made. A fresh session resumes from this file alone.

## Scan results (Phase 0)

| Check | Finding |
|---|---|
| Platform (and: files? shell? symlinks? subagents? per-task model choice? persistent memory?) | _pending_ |
| Native skills directory? (path, or "none") | _pending_ |
| User's workspace (notes vault / project folder / git repo / shared repo) | _pending_ |
| Existing standing instructions file (path — and read it) | _pending_ |
| Existing skills, prompts or commands folder | _pending_ |
| Existing project/status folder habit? (steers DP-3) | _pending_ |

## Decisions (Phase 1)

| # | Decision | Choice | Notes |
|---|---|---|---|
| DP-1 | Where skills live + how they're discovered | _pending_ | |
| DP-2 | Ordering prefix on folders | _pending_ | |
| DP-3 | Companion project folder | _pending_ | |
| DP-4 | Dry-run gate | _pending_ | |
| DP-5 | Invocation method | _pending_ | |
| DP-6 | Interview mode | _pending_ | |

## Tasks

### Phase 0 — Environment scan
- [ ] Platform + capabilities identified
- [ ] Skills directory checked (found, or confirmed absent)
- [ ] Workspace located (or chat-only path confirmed)
- [ ] Existing instructions file read
- [ ] Scan results recorded above

### Phase 1 — Decisions
- [ ] User oriented with `EXAMPLE-SKILL.md`
- [ ] DP-1 through DP-6 decided and recorded above

### Phase 2 — Install
- [ ] `skill-creation` installed where the platform discovers it (or kept as the paste-in document)
- [ ] Discovery verified using the check for this DP-1 branch (A: `ls -lL` through the link · B: file exists · C: file exists **and** the routing line names its path · D: findable from the index) — record which check ran
- [ ] Skills index `README.md` created from the template at the DP-1 location (path recorded below) — library table seeded, Install record filled in with all six decisions
- [ ] `templates/SKILL-TEMPLATE.md` installed as a loose file, not inside a discovery directory (or declined — record which)
- [ ] Standing instructions file wired, and the user told which file it landed in
- [ ] Install paths recorded here

| Install paths | |
|---|---|
| Skills folder | _pending_ |
| Discovery entry | _pending_ |
| Skills index | _pending_ |
| Instructions file wired | _pending_ |
| Companion project folder parent (if DP-3 ≠ never) | _pending_ |

### Phase 3 — First live build
- [ ] Informal store found in Phase 0 (old prompts, snippets) triaged through the Step 0 gate for candidates
- [ ] Real candidate chosen with the user
- [ ] Any candidate that failed the gate: right container built instead, and recorded below
- [ ] Step 0 gate run and the verdict said out loud
- [ ] Interview run (per DP-6), spec block confirmed
- [ ] Skill folder written; discovery verified
- [ ] Test prompts generated and approved by the user
- [ ] Dry-run gate run (per DP-4) — all four checklist items reported, value check included
- [ ] Skill registered in the skills index
- [ ] Acceptance test: user ran a fresh session and reported back

| Gate-failed candidates (if any) | |
|---|---|
| Candidate | _none / description_ |
| Verdict + what got built instead | _pending_ |

| First skill | |
|---|---|
| Skill name + path | _pending_ |
| Tier + DBS layers | _pending_ |
| Dry-run verdict (incl. value check) | _pending_ |
| Acceptance prompt used | _pending_ |
| Did it fire? Was the output right? | _pending_ |

### Phase 4 — Wrap up
- [ ] **This file copied into the workspace first** — next to the skills index as `install-record-STATUS.md`, or its decisions folded into the index. It does not survive deleting the kit repo, and nothing else in this phase is unrecoverable
- [ ] Install record in the skills index confirmed complete — every row, including the literal verification command and the subagent / model-choice row
- [ ] This file fully ticked; Blockers emptied or what remains stated
- [ ] User walked through what's installed and where, with the wired instructions file named

## Blockers

- none
