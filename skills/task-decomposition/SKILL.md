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
Each task is atomic, with acceptance criteria, dependencies, and complexity estimate.

## Instructions

1. Read PRD from `docs/discovery/NN-<slug>/prd.md` (passed as argument or selected from recent in `docs/discovery/`)
2. Read `@docs/system-context.md` to understand existing code
3. Break down into tasks, grouping by roles:
   - **Infrastructure** — IaC, CI/CD, environments, IAM
   - **Backend** — API, business logic, integrations
   - **Data** — models, migrations, indexes, seed data
   - **Frontend** — UI components, pages, forms (if applicable)
4. For each task, fill in the [task template](templates/task.md)
5. Determine dependencies between tasks (which task blocks which)
6. Arrange execution sequence by waves:
   - Wave 1: tasks with no dependencies (can run in parallel)
   - Wave 2: depend on Wave 1
   - Wave 3+: and so on
7. Save to `docs/discovery/NN-<slug>/tasks/`:
   - `INDEX.md` — summary file with execution order
   - `TASK-NNN.md` — separate file for each task
8. Update status in `docs/discovery/INDEX.md` to `Approved`

## Important

- Task is **atomic** — one developer picks it up and does it without asking "what does this mean?"
- Each task has **concrete acceptance criteria** — can verify done/not done
- Each task lists **files to create/modify**
- Dependencies between tasks are **explicit** — "depends on TASK-XXX"
- Complexity is estimated as S/M/L/XL (Small = hours, Medium = day, Large = 2-3 days, XL = week)
- Each task references a specific functional requirement from PRD (FR-N)

## Supporting files

- [templates/task.md](templates/task.md) — task template
