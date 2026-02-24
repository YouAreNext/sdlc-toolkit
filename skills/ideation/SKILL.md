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
2. Read `@docs/discovery/INDEX.md` if it exists — determine the next iteration number (NN)
3. Ask the user up to 5 clarifying questions in Mom Test style:
   - What problem are we solving? Who faces it?
   - What does the user currently do instead?
   - What are the constraints (technical, time, budget)?
   - What is definitely NOT in scope?
   - How will we know it worked? (success metrics)
4. **Generate a slug** from the idea title — short, hyphenated, lowercase, English only
   - Example: "Article Reading Feature" → `article-reading`
   - Example: "User Authentication" → `user-auth`
5. **Create the iteration folder first**: `docs/discovery/NN-<slug>/`
   - Example: `docs/discovery/01-article-reading/`
   - Example: `docs/discovery/02-user-auth/`
6. Generate Idea Brief content using the [template](templates/idea-brief.md)
7. Save as `docs/discovery/NN-<slug>/idea-brief.md` (INSIDE the folder, not next to it!)
8. Create or update `docs/discovery/INDEX.md` — add a new row with status `Discovery`

## Critical: Folder Structure

**CORRECT structure:**
```
docs/discovery/
├── INDEX.md
└── 01-article-reading/      ← folder for iteration
    └── idea-brief.md        ← file inside folder
```

**WRONG structure (don't do this):**
```
docs/discovery/
├── INDEX.md
└── 01-idea-brief.md         ← WRONG! No folder created
```

## Important

- Idea Brief must be **less than 100 lines**
- Don't go into implementation details — that's the PRD's job
- If system-context exists, consider what's already implemented
- Slug must be short, hyphenated, lowercase, English (e.g.: `article-processing`, `user-auth`)
- **Always create the folder first**, then save idea-brief.md inside it

## Supporting files

- [templates/idea-brief.md](templates/idea-brief.md) — Idea Brief template
