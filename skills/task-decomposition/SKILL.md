---
name: task-decomposition
description: >
  Break down a PRD into developer tasks with dependencies, acceptance criteria,
  and role assignments. Generates ordered task list ready for sprint planning.
  Triggers: "decompose", "нарезать задачи", "task breakdown", "разбить на задачи"
allowed-tools: Read, Grep, Glob, Write
---

# Task Decomposition Skill

Нарежь PRD на конкретные задачи для разработчиков.
Каждая задача — атомарная, с acceptance criteria, зависимостями и оценкой сложности.

## Инструкции

1. Прочитай PRD из `docs/discovery/NN-<slug>/prd.md` (передаётся как аргумент или выбирается из последних в `docs/discovery/`)
2. Прочитай `@docs/system-context.md` для понимания существующего кода
3. Разбей на задачи, группируя по ролям:
   - **Infrastructure** — IaC, CI/CD, environments, IAM
   - **Backend** — API, бизнес-логика, интеграции
   - **Data** — модели, миграции, индексы, seed data
   - **Frontend** — UI компоненты, страницы, формы (если применимо)
4. Для каждой задачи заполни [шаблон задачи](templates/task.md)
5. Определи зависимости между задачами (какая задача блокирует какую)
6. Выстрой последовательность выполнения по волнам (waves):
   - Wave 1: задачи без зависимостей (можно параллельно)
   - Wave 2: зависят от Wave 1
   - Wave 3+: и так далее
7. Сохрани в `docs/discovery/NN-<slug>/tasks/`:
   - `INDEX.md` — сводный файл с execution order
   - `TASK-NNN.md` — отдельный файл для каждой задачи
8. Обнови статус в `docs/discovery/INDEX.md` на `Approved`

## Важно

- Задача **атомарная** — один разработчик берёт и делает, не задавая вопросов «а что имеется в виду?»
- У каждой задачи **конкретные acceptance criteria** — можно проверить done/not done
- У каждой задачи указаны **файлы для создания/изменения**
- Зависимости между задачами **явные** — "depends on TASK-XXX"
- Complexity оценивается как S/M/L/XL (Small = часы, Medium = день, Large = 2-3 дня, XL = неделя)
- Каждая задача ссылается на конкретное функциональное требование из PRD (FR-N)

## Supporting files

- [templates/task.md](templates/task.md) — шаблон задачи
