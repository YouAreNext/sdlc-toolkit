---
name: update-context
description: >
  Update system-context.md after a feature is implemented.
  Scans codebase and PRD to reflect current system state.
  Triggers: "update context", "refresh context", after feature completion
allowed-tools: Read, Grep, Glob, Write, Bash
---

# Update Context Skill

Update system-context.md after feature implementation.
The skill scans the codebase and PRD, reflecting the current system state.

## Instructions

1. Read current `@docs/system-context.md`
2. Determine what changed:
   - Scan codebase for new files, services, API endpoints
   - Check for new/modified data models
   - Check for infrastructure changes (IaC, configs)
3. Read the last implemented PRD from `docs/discovery/NN-<slug>/prd.md`
4. If `docs/system-context.md` doesn't exist — create it using the [template](templates/system-context.md)
5. Update `docs/system-context.md`, adding:
   - New components/services to Components section
   - New API endpoints to API Endpoints table
   - New/modified data models to Data Models section
   - Infrastructure changes to Infrastructure section
   - New row to Implemented Features table
   - Key decisions to Key Decisions table
   - Link to PRD as the source of the decision
6. **DO NOT delete** existing information — only add/update
7. Update "Last updated" date in system-context.md
8. Update iteration status in `docs/discovery/INDEX.md` to `Done`

## Important

- system-context.md is an **append-only** document (information is added, not removed)
- Each change must include a reference to the source PRD ("Added in: PRD-slug")
- After updating, the next `/prd` should correctly see what's already implemented
- Architecture Overview is updated incrementally — add new components, don't rewrite everything

## Supporting files

- [templates/system-context.md](templates/system-context.md) — initial system-context template
