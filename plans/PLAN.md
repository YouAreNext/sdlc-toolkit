# SDLC Toolkit — Project Plan

## Goal

Claude Code **plugin** with a set of skills, commands, and hooks covering the MVP development cycle: **Ideation → PRD → Task Decomposition → Development**. Distributed via plugin marketplace. Tested on a real project — AI Newsletter Pipeline (Slack/Telegram → Article Processing → AI Enrichment → Newsletter Generation).

---

## Repository Structure

```
sdlc-toolkit/                              # Plugin root
├── .claude-plugin/
│   └── plugin.json                        # Plugin manifest (name, version, description)
│
├── skills/                                # Agent Skills (at plugin root level)
│   ├── ideation/
│   │   ├── SKILL.md                       # Phase 1: idea packaging (optional)
│   │   └── templates/
│   │       └── idea-brief.md              # Idea Brief template
│   ├── prd/
│   │   ├── SKILL.md                       # Phase 2: PRD generation
│   │   └── templates/
│   │       ├── prd.md                     # PRD template
│   │       └── discovery-index.md         # Discovery Index template
│   ├── task-decomposition/
│   │   ├── SKILL.md                       # Phase 3: task breakdown
│   │   └── templates/
│   │       └── task.md                    # Task template
│   └── update-context/
│       ├── SKILL.md                       # Closing the loop: system-context update
│       └── templates/
│           └── system-context.md          # Initial system-context template
│
├── commands/                              # Slash commands
│   ├── ideate.md                          # /sdlc-toolkit:ideate $ARGUMENTS
│   ├── prd.md                             # /sdlc-toolkit:prd $ARGUMENTS
│   ├── decompose.md                       # /sdlc-toolkit:decompose $ARGUMENTS
│   └── update-context.md                  # /sdlc-toolkit:update-context
│
├── hooks/
│   └── hooks.json                         # Hooks (protected branch protection)
│
├── README.md                              # Description, installation, quick start
├── CLAUDE.md                              # Example CLAUDE.md for consumer project
└── PLAN.md                                # Project plan (for development)
```

**Created by skills in user's project** (NOT part of the plugin):
```
project/
├── docs/
│   ├── system-context.md                  # "Project memory"
│   └── discovery/
│       ├── INDEX.md                       # Iteration registry
│       └── NN-slug/                       # One discovery iteration
│           ├── idea-brief.md
│           ├── prd.md
│           ├── decisions.md
│           └── tasks/
```

---

## Phase 0: Foundation

### 0.1 CLAUDE.md

The toolkit's root file — describes **the toolkit itself**, not the target project.

```markdown
# SDLC Toolkit

Modular set of Claude Code skills for the full development cycle.

## Workflow
Ideation → PRD → Task Decomposition → Development → Update Context → (repeat)

## Conventions
- All output artifacts are markdown in docs/ or alongside the code
- system-context.md is the single source of truth about the current project state
- Each iteration lives in a separate folder docs/discovery/NN-<slug>/
- Discovery artifacts (idea brief, PRD, tasks, decisions) are grouped by iteration
- Final artifacts after approval update system docs (docs/api/, docs/models/) via /update-context
- docs/discovery/INDEX.md is the registry of all iterations with statuses
- PRD always reads @docs/system-context.md before generation
- Tasks contain acceptance criteria and dependencies
- After feature implementation — mandatory /update-context

## Skills
- /sdlc-toolkit:ideate — (optional) package an idea into an idea brief (<100 lines)
- /sdlc-toolkit:prd — generate PRD from idea brief, description, or system context
- /sdlc-toolkit:decompose — break down PRD into tasks by roles
- /sdlc-toolkit:update-context — update system-context.md after implementation

## Important
- @docs/system-context.md — current architecture and state
- @docs/discovery/INDEX.md — registry of all discovery iterations
```

When installing the plugin in a target project — the project's CLAUDE.md is adapted, adding stack, build commands, and conventions.

### 0.2 Hooks

`hooks/hooks.json` — plugin hooks (automatically applied on installation):

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [{
          "type": "command",
          "command": "branch=$(cd $CLAUDE_PROJECT_DIR && git branch --show-current 2>/dev/null); if [ \"$branch\" = \"main\" ] || [ \"$branch\" = \"production\" ]; then echo '{\"decision\":\"block\",\"reason\":\"Cannot edit on protected branch. Create a feature branch first.\"}'; fi"
        }]
      }
    ]
  }
}
```

Auto-lint is added after selecting the target project's language.

---

## Phase 1: Ideation Skill

### Purpose

Package a raw idea / conversation / description into a structured **Idea Brief** — maximum 100 lines. This is NOT a PRD, it's a "napkin sketch" that formalizes What and Why, without touching How.

### Skill: `skills/ideation/SKILL.md`

```yaml
---
name: ideation
description: >
  Structure a raw idea into a concise Idea Brief (<100 lines).
  Use when the user describes a new product, feature, or system
  and needs it formalized before writing a PRD.
  Triggers: "ideate", "idea brief", "formalize idea", "package idea"
allowed-tools: Read, Grep, Glob, Write
---
```

**SKILL.md content — instructions:**

1. Read `@docs/system-context.md` if exists (understand what already exists)
2. Read `@docs/discovery/INDEX.md` if exists (determine next iteration number)
3. Ask up to 5 clarifying questions in Mom Test style (what are we solving, for whom, what constraints)
4. Generate Idea Brief using the [template](templates/idea-brief.md)
5. Determine next iteration number (NN) from `docs/discovery/INDEX.md` or start with 01
6. Create folder `docs/discovery/NN-<slug>/` and save `idea-brief.md` there
7. Update `docs/discovery/INDEX.md` — add new row with status `Discovery`

### Template: `skills/ideation/templates/idea-brief.md`

```markdown
# Idea Brief: [Title]

## Problem Statement
[2-3 sentences: what pain are we solving, for whom]

## Proposed Solution
[3-5 sentences: what we're doing at a high level]

## Key User Flows
1. [Main flow]
2. [Second flow if applicable]

## Scope Boundaries
- IN: [what's included]
- OUT: [what's explicitly not in MVP]

## Success Criteria
- [Metric 1]
- [Metric 2]

## Known Constraints
- [Technical]
- [Business]
- [Timeline]

## Open Questions
- [What still needs to be figured out]
```

### Command: `commands/ideate.md`

```markdown
Structure the idea into an Idea Brief.

Use the ideation skill. Input data: $ARGUMENTS

If $ARGUMENTS is empty — ask the user to describe the idea.
If @docs/system-context.md exists — consider the current system state.
```

---

## Phase 2: PRD Skill

### Purpose

Generate a complete PRD from Idea Brief + System Context. Required sections: functional requirements, API contracts, data model, testing.

### Key Mechanism: Accounting for Existing System

The PRD skill **always** starts by reading `@docs/system-context.md`. This solves the "blank slate" problem:
- **First PRD** (system-context empty): generates from scratch, writes "Greenfield" in "Current System State" section
- **Subsequent PRDs**: reads existing APIs, models, services and builds delta — what we're adding/changing

### Skill: `skills/prd/SKILL.md`

```yaml
---
name: prd
description: >
  Generate a Product Requirements Document from an Idea Brief.
  Always reads system-context.md to account for existing system state.
  Includes functional requirements, API contracts, data models, and test strategy.
  Triggers: "prd", "requirements", "write PRD", "product requirements"
allowed-tools: Read, Grep, Glob, Write
---
```

**Instructions:**

1. Read `@docs/system-context.md` — this is the current system state
2. Read Idea Brief from `docs/discovery/NN-<slug>/idea-brief.md` (passed as argument or selected)
3. If system-context is not empty — determine what's already implemented and build PRD as delta
4. Generate PRD using the [template](templates/prd.md)
5. For each API endpoint — specify method, path, request/response schema
6. For each data model — specify fields, types, constraints
7. Testing Strategy section is required — include approach and key scenarios
8. Save to `docs/discovery/NN-<slug>/prd.md` (same folder as idea-brief)
9. Update status in `docs/discovery/INDEX.md` to `In Review`

### Template: `skills/prd/templates/prd.md`

```markdown
# PRD: [Feature Name]

**Status:** Draft | Review | Approved
**Date:** YYYY-MM-DD
**Idea Brief:** [link to idea brief]

## Current System State
[Auto-filled from system-context.md]
[What already exists and how the new feature relates to it]
[If greenfield — state explicitly]

## Overview
[What we're doing and why — 3-5 sentences]

## Functional Requirements

### FR-1: [Title]
- **Description:** [what the system should do]
- **Priority:** P0 | P1 | P2
- **Acceptance Criteria:**
  - [ ] [Criterion 1]
  - [ ] [Criterion 2]

### FR-2: [Title]
...

## API Contracts

### [POST /api/v1/resource]
- **Description:** [purpose]
- **Auth:** [requirements]
- **Request Body:**
```json
{
  "field": "type — description"
}
```
- **Response 200:**
```json
{
  "field": "type — description"
}
```
- **Error Codes:** 400, 401, 404, 500 — [when each occurs]

## Data Models

### [ModelName]
| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | UUID | yes | Primary key |
| ... | ... | ... | ... |

**Indexes:** [list of indexes]
**Relations:** [relationships with other models]

## System Changes
[What changes in the existing system]
- New services: [list]
- Modified services: [list with change descriptions]
- New infrastructure: [what needs to be created]
- Modified infrastructure: [what needs to be changed]

## Non-Functional Requirements
- **Performance:** [target metrics]
- **Scalability:** [expected load]
- **Security:** [requirements]
- **Observability:** [logging, monitoring, alerts]

## Testing Strategy
- **Unit tests:** [what we cover, target coverage]
- **Integration tests:** [key scenarios]
- **E2E scenarios:** [critical path]

## Dependencies & Risks
| Risk | Impact | Mitigation |
|------|--------|------------|
| ... | ... | ... |

## Out of Scope
[What we're explicitly not doing in this PRD]
```

---

## Phase 3: Task Decomposition Skill

### Purpose

Break down PRD into concrete tasks for developers. Each task is atomic, with acceptance criteria, dependencies, and complexity estimate.

### Skill: `skills/task-decomposition/SKILL.md`

```yaml
---
name: task-decomposition
description: >
  Break down a PRD into developer tasks with dependencies, acceptance criteria,
  and role assignments. Generates ordered task list ready for sprint planning.
  Triggers: "decompose", "break down tasks", "task breakdown", "split into tasks"
allowed-tools: Read, Grep, Glob, Write
---
```

**Instructions:**

1. Read PRD from `docs/discovery/NN-<slug>/prd.md` (passed as argument or selected)
2. Read `@docs/system-context.md` to understand existing code
3. Break down into tasks, grouping by:
   - **Infrastructure** (IaC, CI/CD, environments)
   - **Backend** (API, business logic, integrations)
   - **Data** (models, migrations, indexes)
   - **Frontend** (UI, if applicable)
4. For each task fill in the [template](templates/task.md)
5. Determine dependencies between tasks (which task blocks which)
6. Arrange execution sequence
7. Save to `docs/discovery/NN-<slug>/tasks/` (INDEX.md + individual TASK-NNN.md files)
8. Update status in `docs/discovery/INDEX.md` to `Approved`

### Template: `skills/task-decomposition/templates/task.md`

```markdown
# TASK-[NNN]: [Title]

**Role:** Backend | Frontend | Infrastructure | Data
**Priority:** P0 | P1 | P2
**Complexity:** S | M | L | XL
**Depends on:** TASK-XXX, TASK-YYY (or "none")
**PRD Reference:** FR-N [link to requirement]

## Description
[What needs to be done — concrete, no abstractions]

## Acceptance Criteria
- [ ] [Concrete verifiable criterion]
- [ ] [Another one]
- [ ] Tests written and passing

## Technical Notes
[Implementation hints, recommended approaches, files to modify]

## Files to Create/Modify
- `path/to/new/file.py` — [purpose]
- `path/to/existing/file.py` — [what we're changing]
```

### Template: `skills/prd/templates/discovery-index.md`

```markdown
# Discovery Index

**Project:** [Project Name]
**Last updated:** YYYY-MM-DD

| # | Slug | Status | Date Started | Summary |
|---|------|--------|--------------|---------|
| 01 | example-feature | Done | YYYY-MM-DD | Brief description of the iteration |
```

Statuses: `Discovery` → `In Review` → `Approved` → `Done`

### Output Artifact: Task Index

In addition to individual task files — a summary `docs/discovery/NN-<slug>/tasks/INDEX.md` is generated:

```markdown
# Tasks: [Feature Name]
**PRD:** [link]
**Total:** N tasks (X backend, Y infra, Z data)

## Execution Order

### Wave 1 (parallel, no dependencies)
- [ ] TASK-001: Setup OpenSearch index schema [Data, S]
- [ ] TASK-002: Create Lambda skeleton + IAM roles [Infra, M]

### Wave 2 (depends on Wave 1)
- [ ] TASK-003: Implement URL parser service [Backend, M] → depends: TASK-002
- [ ] TASK-004: Implement web scraper [Backend, L] → depends: TASK-002

### Wave 3
- [ ] TASK-005: Integration: parser → scraper → storage [Backend, L] → depends: TASK-003, TASK-004
...
```

---

## Phase 4: Closing the Loop — Update Context

### Purpose

After implementing a feature, the developer runs `/update-context` — the skill reads current code, PRD, and updates `docs/system-context.md`.

### Skill: `skills/update-context/SKILL.md`

```yaml
---
name: update-context
description: >
  Update system-context.md after a feature is implemented.
  Scans codebase and PRD to reflect current system state.
  Triggers: "update context", "refresh context", after feature completion
allowed-tools: Read, Grep, Glob, Write, Bash
---
```

**Instructions:**

1. Read current `@docs/system-context.md`
2. Determine what changed (new files, APIs, models, services)
3. Read the last implemented PRD from `docs/discovery/NN-<slug>/prd.md`
4. Update system-context.md, adding:
   - New components/services
   - New API endpoints
   - New/modified data models
   - Infrastructure changes
   - Link to PRD as the source of the decision
5. DO NOT delete existing information, only add/update
6. Update iteration status in `docs/discovery/INDEX.md` to `Done`

### Template: `docs/system-context.md`

```markdown
# System Context

**Last updated:** YYYY-MM-DD
**Project:** [Name]

## Architecture Overview
[High-level system description — updated as the system grows]

## Components

### [Component Name]
- **Type:** Lambda | Service | Database | Queue | ...
- **Purpose:** [what it does]
- **Added in:** PRD-[slug]

## API Endpoints
| Method | Path | Service | Description | Added in |
|--------|------|---------|-------------|----------|
| POST | /api/v1/... | ... | ... | PRD-xxx |

## Data Models
### [ModelName]
| Field | Type | Description | Added in |
|-------|------|-------------|----------|
| ... | ... | ... | PRD-xxx |

## Infrastructure
- **Compute:** [Lambda, ECS, ...]
- **Storage:** [OpenSearch, S3, DynamoDB, ...]
- **Messaging:** [EventBridge, SQS, ...]
- **Monitoring:** [CloudWatch, ...]

## Implemented Features
| # | Feature | PRD | Date | Status |
|---|---------|-----|------|--------|
| 1 | ... | [link] | ... | Done |

## Key Decisions
| Decision | Rationale | PRD | Date |
|----------|-----------|-----|------|
| OpenSearch for articles | Full-text search + embeddings | PRD-001 | ... |
```

---

## Implementation Order

```
Milestone 1: Skeleton                          [Week 1, days 1-2]
├── Create plugin structure (.claude-plugin/, skills/, commands/, hooks/)
├── Write plugin.json manifest
├── Write CLAUDE.md (example for consumer project)
├── Create templates inside skills/*/templates/
└── Write README.md with installation instructions

Milestone 2: Ideation Skill                    [Week 1, days 2-3]
├── Write skills/ideation/SKILL.md
├── Write commands/ideate.md
├── Test: claude --plugin-dir ./ + /sdlc-toolkit:ideate
├── Verify output on AI Newsletter idea
└── Iterate skill description based on output quality

Milestone 3: PRD Skill                         [Week 1, days 3-5]
├── Write skills/prd/SKILL.md
├── Write commands/prd.md
├── Test: /sdlc-toolkit:prd for Step Function 1 (Article Processing)
├── Verify: Are API contracts concrete? Are acceptance criteria verifiable?
├── Verify: Is discovery folder created automatically?
└── Iterate

Milestone 4: Task Decomposition Skill          [Week 2, days 1-2]
├── Write skills/task-decomposition/SKILL.md
├── Write commands/decompose.md
├── Test: /sdlc-toolkit:decompose for PRD Article Processing
├── Verify: Are there dependencies? acceptance criteria? atomic?
└── Iterate

Milestone 5: Update Context Skill              [Week 2, day 3]
├── Write skills/update-context/SKILL.md
├── Write commands/update-context.md
├── Simulate SF1 implementation → /sdlc-toolkit:update-context
├── Verify: Did status in discovery/INDEX.md update to Done?
└── Verify: Does the next /sdlc-toolkit:prd see that SF1 is already implemented?

Milestone 6: Full Cycle Validation             [Week 2, days 4-5]
├── Run full cycle for Step Function 2 (AI Enrichment):
│   ├── /sdlc-toolkit:ideate → docs/discovery/02-ai-enrichment/idea-brief.md
│   ├── /sdlc-toolkit:prd → docs/discovery/02-ai-enrichment/prd.md
│   ├── /sdlc-toolkit:decompose → docs/discovery/02-ai-enrichment/tasks/
│   └── /sdlc-toolkit:update-context → updated system-context + INDEX.md
├── Document pain points and gaps
├── Update README with lessons learned
└── Create issues for future skills (review, testing, SA)
```

---

## Future Phases (post-MVP)

| Phase | Skill/Artifact | Priority |
|-------|----------------|----------|
| Code Review | Subagent `reviewer.md` + PostToolUse hook | High |
| Testing | TDD skill + auto-test hook | High |
| Architecture | Subagent `architect.md` (read-only, Opus) | Medium |
| Business Requirements | BR skill between ideation and PRD | Medium |
| Solution Architecture | SA skill between PRD and tasks | Medium |
| Sprint Planning | Task grouping into sprints | Low |
| Retrospective | Skill for analyzing what went wrong | Low |
| Onboarding | `/onboard` command for team newcomers | Low |

---

## MVP Success Criteria

1. **Full cycle completes in <1 hour** for one feature (ideation → tasks)
2. **Second PRD accounts for the first** — system-context correctly reflects what's implemented
3. **Tasks are atomic** — developer picks up a task without asking "what does this mean?"
4. **Artifacts in git** — everything is versioned, reviewable, traceable
5. **Portability** — toolkit can be copied to another project and adapted in 30 minutes
