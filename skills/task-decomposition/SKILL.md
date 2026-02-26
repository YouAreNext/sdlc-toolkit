---
name: task-decomposition
description: >
  Break down a PRD into developer tasks with dependencies, acceptance criteria,
  and role assignments. Generates ordered task list ready for sprint planning.
  Triggers: "decompose", "break down tasks", "task breakdown", "split into tasks"
allowed-tools: Read, Grep, Glob, Write
---

# Task Decomposition Skill

Break down the PRD into concrete tasks for developers.
Each task is a meaningful deliverable with acceptance criteria, dependencies, and complexity estimate.

## Instructions

1. Read PRD from `docs/discovery/NN-<slug>/prd.md` (passed as argument or selected from recent in `docs/discovery/`)
2. Read `@docs/system-context.md` to understand existing code
3. Read ADRs from `docs/discovery/NN-<slug>/decisions/` if they exist — technical decisions affect task structure
4. Break down into tasks, grouping by roles:
   - **Infrastructure** — IaC, CI/CD, environments, IAM
   - **Backend** — API, business logic, integrations
   - **Data** — models, migrations, indexes, seed data
   - **Frontend** — UI components, pages, forms (if applicable)
5. For each task, fill in the [task template](templates/task.md)
6. Determine dependencies between tasks (which task blocks which)
7. Arrange execution sequence by waves:
   - Wave 1: tasks with no dependencies (can run in parallel)
   - Wave 2: depend on Wave 1
   - Wave 3+: and so on
8. Save everything into a single file: `docs/discovery/NN-<slug>/tasks.md`
9. Update status in `docs/discovery/INDEX.md` to `Approved`

## Detail Level

Tasks describe **what** to deliver and **why**, not **how** to code it.
The developer decides class names, file structure, and implementation details during development.

### What to include

- **Goal** — what this task achieves in the system
- **High-level flow** — sequence of operations when relevant (e.g., "receive → validate → transform → store → notify")
- **Specific references** — point to PRD requirements (FR-N) and ADR decisions with section names (e.g., "see PRD section Data Models → Order", "see ADR-002")
- **Product-level AC** — verifiable outcomes, not implementation details

### What NOT to include (anti-patterns)

| Bad (over-specified) | Good (outcome-focused) |
|---|---|
| `src/config.py` — `Settings` class with `lru_cache` | Settings are loaded from env, validated, and cached |
| `DATABASE_URL` as `str` | Database connection is configurable via environment |
| `process(item: Item) -> ProcessResult` | Each item is processed independently with per-item results |
| `Returns stats: total, processed, skipped` | Processing statistics are available after each run |
| `src/services/processor.py` — `ProcessorService` class | _(omit — file/class naming is a development decision)_ |

**Rule of thumb:** if the AC reads like a code review checklist — it's too technical. If it reads like a QA test plan — it's the right level.

### Description style

Description should be **2-3 sentences max**: what the task does + where to find technical details.

Good: "CRUD operations for the orders table. Supports idempotency, filtering, and pagination. Data model — see PRD (section Data Models → Order)."

Bad: long paragraph repeating everything from the PRD with class names and method signatures.

### AC patterns

- **Test AC**: list what to verify, not how — "Unit tests: config parsing, schema creation, validation"
- **Flow AC**: for orchestrator/pipeline tasks, use numbered steps describing the business flow directly in AC
- **Outcome AC**: "App starts, GET /health returns 200" — not "Create main.py with FastAPI instance"

### Task granularity

- Task = **meaningful deliverable** that can be independently verified ("can I demo or test this?")
- If two tasks are always done sequentially by the same person and one can't be tested without the other — **merge them**
- Example: "Project scaffolding" + "App entry point with lifespan" → one task "Project setup with runnable skeleton"
- Don't create separate tasks for things that are natural parts of a bigger task (e.g., don't split "create config" from "validate config" — that's one task)
- **Cross-cutting concerns** (logging, error handling, validation) are NOT separate tasks — they are part of each task's implementation

## Important

- Task is **atomic** — one developer picks it up and does it without asking "what does this mean?"
- AC describes **outcomes**, not implementation — verifiable by running the app or tests
- Dependencies between tasks are **explicit** — "depends on TASK-XXX"
- Complexity is estimated as S/M/L/XL (Small = hours, Medium = day, Large = 2-3 days, XL = week)
- Don't duplicate PRD/ADR specs — **reference** them with specific sections (e.g., "API contracts per PRD FR-3", "technology choice per ADR-001")
- Tasks that relate to a PRD requirement should reference it (FR-N), but infrastructure/setup tasks may not have a direct FR

## Supporting files

- [templates/task.md](templates/task.md) — task template
