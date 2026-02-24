---
name: adr
description: >
  Create an Architecture Decision Record (ADR) to document technical decisions.
  Use when making significant architectural choices that affect the system.
  Triggers: "adr", "decision record", "architecture decision", "document decision"
allowed-tools: Read, Grep, Glob, Write
---

# ADR Skill

Create a structured Architecture Decision Record to document significant technical decisions.

## When to Use

- Choosing between technologies (e.g., PostgreSQL vs MongoDB)
- Defining system boundaries or integration patterns
- Making trade-offs that affect performance, security, or maintainability
- Any decision that future developers will need to understand

## Instructions

1. Read `@docs/system-context.md` — understand current architecture
2. Read `@docs/discovery/INDEX.md` — determine current iteration (if applicable)
3. Ask clarifying questions if needed:
   - What decision are we documenting?
   - What alternatives were considered?
   - What are the constraints?
4. Determine ADR location:
   - If related to a specific iteration → `docs/discovery/NN-slug/decisions/ADR-NNN.md`
   - If cross-project/global → `docs/adr/ADR-NNN.md`
5. Determine next ADR number from existing ADR files in the target directory
6. Generate ADR using the [template](templates/adr.md)
7. Create decisions/ or adr/ folder if it doesn't exist
8. Save ADR file

## Important

- ADR is **immutable** once accepted — if decision changes, create a new ADR that supersedes it
- Include concrete alternatives that were considered
- Be explicit about trade-offs and consequences
- Link to related PRDs or other ADRs

## Supporting files

- [templates/adr.md](templates/adr.md) — ADR template
