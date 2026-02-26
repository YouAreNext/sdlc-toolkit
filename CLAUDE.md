# SDLC Toolkit

Modular set of Claude Code skills for the full development cycle.
Installed as a plugin: skills are available via the `sdlc-toolkit:` namespace.

## Workflow
[Ideation] → [Foundation] → PRD → Task Decomposition → Development → Update Context → (repeat)

Ideation and Foundation are optional phases.
- Ideation: package a raw idea into a structured brief
- Foundation: establish tech stack and project structure (for greenfield projects)
- If both are defined: start directly with /sdlc-toolkit:prd

## Conventions
- All output artifacts are markdown in docs/ or alongside the code
- system-context.md is the single source of truth about the current project state
- Each iteration lives in a separate folder docs/discovery/NN-<slug>/
- Discovery artifacts (idea brief, PRD, tasks, decisions) are grouped by iteration
- Final artifacts after approval update system docs (docs/api/, docs/models/) via /sdlc-toolkit:update-context
- docs/discovery/INDEX.md is the registry of all iterations with statuses
- PRD always reads @docs/system-context.md before generation
- Tasks contain acceptance criteria and dependencies
- After feature implementation — mandatory /sdlc-toolkit:update-context

## Skills
- /sdlc-toolkit:foundation — (greenfield) establish tech stack, project structure, conventions
- /sdlc-toolkit:ideate — (optional) package an idea into an idea brief (<100 lines)
- /sdlc-toolkit:prd — generate PRD from idea brief, description, or system context
- /sdlc-toolkit:adr — create Architecture Decision Record for technical decisions
- /sdlc-toolkit:generate-claude-md — generate CLAUDE.md for a project (under 80 lines, best practices)
- /sdlc-toolkit:decompose — break down PRD into tasks by roles
- /sdlc-toolkit:update-context — update system-context.md after implementation

## Important
- @docs/system-context.md — current architecture and state
- @docs/discovery/INDEX.md — registry of all discovery iterations
