---
name: prd
description: >
  Generate a Product Requirements Document from an Idea Brief or raw description.
  Always reads system-context.md to account for existing system state.
  Includes functional requirements, API contracts, data models, and test strategy.
  Triggers: "prd", "requirements", "write PRD", "product requirements"
allowed-tools: Read, Grep, Glob, Write
---

# PRD Skill

Generate a complete PRD based on an Idea Brief, user description, or System Context.
PRD must include: functional requirements, API contracts, data models, testing strategy.

## Entry Points

PRD can be created from different sources:
- **From Idea Brief** — if the ideation phase is complete, read `docs/discovery/NN-<slug>/idea-brief.md`
- **Directly** — if ideation is skipped, PRD is created from user description or command arguments

## Key Mechanism: Accounting for Existing System

The PRD skill **always** starts by reading system-context.md:
- **First PRD** (system-context empty): generates from scratch, writes "Greenfield" in "Current System State" section
- **Subsequent PRDs**: reads existing APIs, models, services and builds a **delta** — what we're adding/changing

## Instructions

1. Read `@docs/system-context.md` — this is the current system state
2. Read `@docs/discovery/INDEX.md` — determine the next iteration number
3. Determine the requirements source:
   - If path to idea-brief or iteration slug is provided — read Idea Brief from `docs/discovery/NN-<slug>/idea-brief.md`
   - If text description is provided — use it as direct input
   - If arguments are empty — ask the user what we're building
4. If folder `docs/discovery/NN-<slug>/` doesn't exist yet — create it (determine next NN from INDEX.md or start with 01)
5. If `docs/discovery/INDEX.md` doesn't exist — create it using the [template](templates/discovery-index.md)
6. If system-context is not empty — determine what's already implemented and build PRD as delta
7. Generate PRD using the [template](templates/prd.md)
8. For each API endpoint — specify method, path, request/response schema in JSON
9. For each data model — specify fields, types, constraints, indexes
10. Testing Strategy section is required — include approach and key scenarios
11. Save to `docs/discovery/NN-<slug>/prd.md`
12. Update `docs/discovery/INDEX.md` — add or update row, status `In Review`

## Important

- **Every** API endpoint must have a concrete request/response schema — no abstractions
- **Every** functional requirement must have verifiable acceptance criteria
- "Current System State" section is auto-filled from system-context.md
- If greenfield — state it explicitly
- Data Models must include field types, constraints, and relationships between models
- Folder slug should be short, hyphenated, in English (e.g.: `article-processing`, `user-auth`)

## Supporting files

- [templates/prd.md](templates/prd.md) — PRD template
- [templates/discovery-index.md](templates/discovery-index.md) — Discovery Index template
