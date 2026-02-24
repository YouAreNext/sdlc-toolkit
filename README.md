# SDLC Toolkit

Claude Code plugin for the full MVP development cycle: Ideation → PRD → Task Decomposition → Development → Context Updates.

## Installation

### VSCode Extension
Use the `/plugin` command in chat to open the plugin popup, then enter:
```
youarenext/sdlc-toolkit
```
or the full URL:
```
https://github.com/youarenext/sdlc-toolkit
```

### Terminal (CLI)

**For current project only:**
```bash
claude plugin install youarenext/sdlc-toolkit --scope project
```

**Globally (all projects):**
```bash
claude plugin install youarenext/sdlc-toolkit --scope user
```

### Local development
```bash
claude --plugin-dir ./sdlc-toolkit
```

## Quick Start

1. Install the plugin using one of the methods above
2. Adapt your project's `CLAUDE.md` (add stack, conventions)
3. Run `/sdlc-toolkit:prd "feature description"` (or `/sdlc-toolkit:ideate` if the idea is raw)

## Workflow

```
(optional)
/sdlc-toolkit:ideate "idea description"
    ↓ docs/discovery/01-slug/idea-brief.md

/sdlc-toolkit:prd "feature description"          ← directly
/sdlc-toolkit:prd 01-article-processing          ← from Idea Brief
    ↓ docs/discovery/01-slug/prd.md

/sdlc-toolkit:decompose 01-article-processing
    ↓ docs/discovery/01-slug/tasks/

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
│   ├── prd/
│   │   ├── SKILL.md
│   │   └── templates/
│   │       ├── prd.md
│   │       └── discovery-index.md
│   ├── task-decomposition/
│   │   ├── SKILL.md
│   │   └── templates/
│   │       └── task.md
│   └── update-context/
│       ├── SKILL.md
│       └── templates/
│           └── system-context.md
├── commands/
│   ├── ideate.md
│   ├── prd.md
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
| `/sdlc-toolkit:prd` | Generate PRD from Idea Brief, description, or System Context |
| `/sdlc-toolkit:decompose` | Break down PRD into tasks with dependencies |
| `/sdlc-toolkit:update-context` | Update system-context.md after implementation |

## Discovery Structure

Each iteration lives in a separate folder `docs/discovery/NN-slug/` in your project:

```
docs/discovery/01-article-processing/
├── idea-brief.md    # (optional) what and why
├── prd.md           # Detailed requirements
├── decisions.md     # ADR for this iteration
└── tasks/           # Atomic tasks
    ├── INDEX.md
    ├── TASK-001.md
    └── TASK-002.md
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
