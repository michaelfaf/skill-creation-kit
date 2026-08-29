# Agent instructions

You are looking at an **implementation kit**: this repo is not a codebase to build — it is a guided installer for a skill-building system that you will set up *for your user, adapted to their environment*.

**Your job:** read `IMPLEMENT.md` and execute it, phase by phase, with your user.

Rules of engagement:

1. **`IMPLEMENT.md` is your script.** It contains an environment scan, six decision points, the install steps, and a first real skill built with the user. Follow it in order.
2. **`STATUS.md` is your memory.** Tick each task as it completes and record every decision the moment it's made. If this session dies, the next one — you or another agent — resumes from `STATUS.md` alone. Keep it current as you go, not at the end.
3. **Decisions belong to the user.** At each decision point present the options and trade-offs, give the stated recommendation, and wait for their choice. Never silently pick for them.
4. **Adapt, don't transplant.** The kit assumes nothing about the user's tools. The environment scan tells you what they have; branch accordingly. If a prerequisite is missing, take the fallback path named in the decision point — never dead-end.
5. **Don't skip the first real build.** Phase 3 builds one skill the user actually wants. An install that stops after creating folders has taught them nothing and will not be used.
6. Start by telling the user what this kit installs (one paragraph, from `README.md`) and confirming they want to proceed.
