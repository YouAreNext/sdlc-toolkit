# SDLC Toolkit

Claude Code plugin for the full MVP development cycle: Ideation → Foundation → PRD → Task Decomposition → Development → Context Updates.

## Installation

### Step 1: Add Marketplace

In Claude Code interactive session (VSCode or CLI), run:
```
/plugin marketplace add youarenext/sdlc-toolkit
```

### Step 2: Install Plugin

**Option A: Via UI**
```
/plugin
```
Then go to "Discover" tab, find `sdlc-toolkit`, press Enter and choose scope.

**Option B: Via command**
```
/plugin install sdlc-toolkit@youarenext-sdlc-toolkit --scope project
```

### Scope Options
- `--scope user` — globally for all your projects
- `--scope project` — only for current project (shared with team)
- `--scope local` — only for current project (gitignored)

### Local Development
```bash
claude --plugin-dir /path/to/sdlc-toolkit
```

## Quick Start

1. Install the plugin using one of the methods above
2. For greenfield: run `/sdlc-toolkit:foundation` to set up tech stack and project structure
3. Run `/sdlc-toolkit:prd "feature description"` (or `/sdlc-toolkit:ideate` if the idea is raw)

## Workflow

```
(optional — if idea is raw)
/sdlc-toolkit:ideate "idea description"
    ↓ docs/discovery/01-slug/idea-brief.md

(optional — for greenfield projects)
/sdlc-toolkit:foundation
    ↓ .claude/rules/tech-stack.md
    ↓ docs/system-context.md (Tech Stack section)

/sdlc-toolkit:prd "feature description"          ← directly
/sdlc-toolkit:prd 01-article-processing          ← from Idea Brief
    ↓ docs/discovery/01-slug/prd.md

/sdlc-toolkit:decompose 01-article-processing
    ↓ docs/discovery/01-slug/tasks.md

... development ...

/sdlc-toolkit:update-context
    ↓ docs/system-context.md (updated)
```

## Plugin Structure

```
sdlc-toolkit/
├── .claude-plugin/
│   └── plugin.json              # Plugin manifest
├── skills/
│   ├── ideation/
│   │   ├── SKILL.md
│   │   └── templates/
│   │       └── idea-brief.md
│   ├── foundation/
│   │   ├── SKILL.md
│   │   └── templates/
│   │       ├── tech-stack-rules.md
│   │       ├── tech-stack-presets.md
│   │       └── project-structures.md
│   ├── prd/
│   │   ├── SKILL.md
│   │   └── templates/
│   │       ├── prd.md
│   │       └── discovery-index.md
│   ├── task-decomposition/
│   │   ├── SKILL.md
│   │   └── templates/
│   │       └── task.md
│   ├── adr/
│   │   ├── SKILL.md
│   │   └── templates/
│   │       └── adr.md
│   ├── generate-claude-md/
│   │   ├── SKILL.md
│   │   └── templates/
│   │       ├── claude-md.md
│   │       ├── claude-local-md.md
│   │       └── scoped-rule.md
│   └── update-context/
│       ├── SKILL.md
│       └── templates/
│           └── system-context.md
├── commands/
│   ├── ideate.md
│   ├── foundation.md
│   ├── prd.md
│   ├── adr.md
│   ├── generate-claude-md.md
│   ├── decompose.md
│   └── update-context.md
├── hooks/
│   └── hooks.json
└── README.md
```

## Skills

| Command | Description |
|---------|-------------|
| `/sdlc-toolkit:ideate` | (optional) Package a raw idea into an Idea Brief (<100 lines) |
| `/sdlc-toolkit:foundation` | (greenfield) Establish tech stack, project structure, conventions |
| `/sdlc-toolkit:prd` | Generate PRD from Idea Brief, description, or System Context |
| `/sdlc-toolkit:adr` | Create Architecture Decision Record (ADR) for technical decisions |
| `/sdlc-toolkit:generate-claude-md` | Generate a CLAUDE.md for the current project (under 80 lines, best practices) |
| `/sdlc-toolkit:decompose` | Break down PRD into tasks with dependencies |
| `/sdlc-toolkit:update-context` | Update system-context.md after implementation |

## Discovery Structure

Each iteration lives in a separate folder `docs/discovery/NN-slug/` in your project:

```
docs/discovery/01-article-processing/
├── idea-brief.md    # (optional) what and why
├── prd.md           # Detailed requirements
├── decisions/       # Architecture Decision Records
│   └── ADR-001.md
└── tasks.md         # All tasks with waves and dependencies
```

Iteration statuses: `Discovery` → `In Review` → `Approved` → `Done`

## Philosophy

Instead of chaotic "write me code" — a structured process:
1. **Idea** (optional) is formalized into an Idea Brief
2. **Requirements** are detailed in a PRD with API contracts and data models
3. **Tasks** are broken down atomically with dependencies and acceptance criteria
4. **Context** is updated after implementation — the next iteration sees what's already done

## Adapting for Your Project

In your project's `CLAUDE.md` add:
- Project stack (language, frameworks, infrastructure)
- Build and test commands
- Naming conventions and code style
- Reference to the plugin: `sdlc-toolkit` installed, commands via `/sdlc-toolkit:*`
