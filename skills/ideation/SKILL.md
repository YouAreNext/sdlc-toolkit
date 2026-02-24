---
name: ideation
description: >
  Structure a raw idea into a concise Idea Brief (<100 lines).
  Use when the user describes a new product, feature, or system
  and needs it formalized before writing a PRD.
  Triggers: "ideate", "idea brief", "formalize idea", "package idea"
allowed-tools: Read, Grep, Glob, Write
---

# Ideation Skill

> **Optional phase.** Ideation is useful for complex or vague ideas, but not required.
> If the idea is already clear — you can proceed directly to `/prd`.

Package the user's raw idea into a structured Idea Brief — maximum 100 lines.
Idea Brief is NOT a PRD. It's a "napkin sketch" that formalizes **What** and **Why**, without touching How.

## Instructions

1. Read `@docs/system-context.md` if it exists — understand what already exists in the system
2. Read `@docs/discovery/INDEX.md` if it exists — determine the next iteration number
3. Ask the user up to 5 clarifying questions in Mom Test style:
   - What problem are we solving? Who faces it?
   - What does the user currently do instead?
   - What are the constraints (technical, time, budget)?
   - What is definitely NOT in scope?
   - How will we know it worked? (success metrics)
4. Generate Idea Brief using the [template](templates/idea-brief.md)
5. Determine the next iteration number (NN) from `docs/discovery/INDEX.md` or start with 01
6. Create folder `docs/discovery/NN-<slug>/` and save `idea-brief.md` there
7. Update `docs/discovery/INDEX.md` — add a new row with status `Discovery`

## Important

- Idea Brief must be **less than 100 lines**
- Don't go into implementation details — that's the PRD's job
- If system-context exists, consider what's already implemented
- Folder slug should be short, hyphenated, in English (e.g.: `article-processing`, `user-auth`)

## Supporting files

- [templates/idea-brief.md](templates/idea-brief.md) — Idea Brief template
