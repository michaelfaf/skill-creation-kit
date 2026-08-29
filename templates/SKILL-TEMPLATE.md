---
name: SKILL_NAME
description: Use when USER_INTENT. Triggers — "PHRASE ONE", "PHRASE TWO", "PHRASE THREE". Also use when SECONDARY_SITUATION.
---

<!-- TEMPLATE. Copy this file to your skills folder as <skill-name>/SKILL.md and replace every ALL_CAPS
     placeholder. Delete these comments as you go — a shipped skill has none left.

     name:        lowercase letters, digits, hyphens only. Must equal the folder name.
     description: 1-1024 chars. Say WHEN to use it, never how it works. Third person,
                  starts with "Use when", loaded with the phrases the user would really say.
                  If you find yourself summarising the workflow here, stop: the AI will
                  follow the description and skip the body. -->

TWO OR THREE SENTENCES: what this skill does, what it produces, and the one thing it gets right that an unassisted AI would get wrong.

## Step 1: STEP NAME

WHAT TO DO. One action per step, in order.

## Step 2: STEP NAME

WHAT TO DO.

<!-- Point at a Blueprint at the exact step that needs it, not up front:
     "Read `references/FILE.md` now." Loading it early wastes the context you saved
     by putting it in a file. -->

## Step 3: STEP NAME

WHAT TO DO.

<!-- Point at a Solution the same way:
     "Run `scripts/SCRIPT.py INPUT` — it produces OUTPUT." Scripts exist for output
     that must be identical every run, or for calls the AI cannot make itself. -->

## Edge cases

<!-- Tier 2 and up. Delete for Tier 1. -->

- **Input missing or incomplete:** WHAT TO DO.
- **Input ambiguous:** WHAT TO DO.
- **A step fails twice:** don't loop — say what failed, take the fallback, tell the user.

## Hard rules

<!-- The short list of things that must never happen. Keep it to what actually matters;
     a rule for everything is a rule for nothing. -->

- RULE.
- RULE.
- RULE.
