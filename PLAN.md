# SDLC Toolkit — план проекта

## Цель

Claude Code **плагин** с набором skills, commands и hooks, покрывающих MVP-цикл разработки: **Ideation → PRD → Task Decomposition → Development**. Распространяется через plugin marketplace. Тестируется на реальном проекте — AI Newsletter Pipeline (Slack/Telegram → Article Processing → AI Enrichment → Newsletter Generation).

---

## Структура репозитория

```
sdlc-toolkit/                              # Plugin root
├── .claude-plugin/
│   └── plugin.json                        # Plugin manifest (name, version, description)
│
├── skills/                                # Agent Skills (на корневом уровне плагина)
│   ├── ideation/
│   │   ├── SKILL.md                       # Фаза 1: упаковка идеи (опционально)
│   │   └── templates/
│   │       └── idea-brief.md              # Шаблон Idea Brief
│   ├── prd/
│   │   ├── SKILL.md                       # Фаза 2: генерация PRD
│   │   └── templates/
│   │       ├── prd.md                     # Шаблон PRD
│   │       └── discovery-index.md         # Шаблон Discovery Index
│   ├── task-decomposition/
│   │   ├── SKILL.md                       # Фаза 3: нарезка задач
│   │   └── templates/
│   │       └── task.md                    # Шаблон задачи
│   └── update-context/
│       ├── SKILL.md                       # Замыкание: обновление system-context
│       └── templates/
│           └── system-context.md          # Начальный шаблон system-context
│
├── commands/                              # Slash commands
│   ├── ideate.md                          # /sdlc-toolkit:ideate $ARGUMENTS
│   ├── prd.md                             # /sdlc-toolkit:prd $ARGUMENTS
│   ├── decompose.md                       # /sdlc-toolkit:decompose $ARGUMENTS
│   └── update-context.md                  # /sdlc-toolkit:update-context
│
├── hooks/
│   └── hooks.json                         # Хуки (защита protected branches)
│
├── README.md                              # Описание, установка, quick start
├── CLAUDE.md                              # Пример CLAUDE.md для проекта-потребителя
└── PLAN.md                                # План проекта (для разработки)
```

**Создаётся скиллами в проекте пользователя** (НЕ часть плагина):
```
project/
├── docs/
│   ├── system-context.md                  # «Память проекта»
│   └── discovery/
│       ├── INDEX.md                       # Реестр итераций
│       └── NN-slug/                       # Одна discovery-итерация
│           ├── idea-brief.md
│           ├── prd.md
│           ├── decisions.md
│           └── tasks/
```

---

## Фаза 0: Фундамент

### 0.1 CLAUDE.md

Корневой файл тулкита — описывает **сам тулкит**, не целевой проект.

```markdown
# SDLC Toolkit

Модульный набор Claude Code skills для полного цикла разработки.

## Workflow
Ideation → PRD → Task Decomposition → Development → Update Context → (repeat)

## Conventions
- Все output-артефакты — markdown в docs/ или рядом с кодом
- system-context.md — single source of truth о текущем состоянии проекта
- Каждая итерация живёт в отдельной папке docs/discovery/NN-<slug>/
- Discovery-артефакты (idea brief, PRD, tasks, decisions) группируются по итерации
- Финальные артефакты после апрува обновляют системные docs (docs/api/, docs/models/) через /update-context
- docs/discovery/INDEX.md — реестр всех итераций со статусами
- PRD всегда читает @docs/system-context.md перед генерацией
- Задачи содержат acceptance criteria и зависимости
- После реализации фичи — обязательный /update-context

## Skills
- /sdlc-toolkit:ideate — (опционально) упаковка идеи в idea brief (<100 строк)
- /sdlc-toolkit:prd — генерация PRD из idea brief, описания или system context
- /sdlc-toolkit:decompose — нарезка PRD на задачи по ролям
- /sdlc-toolkit:update-context — обновление system-context.md после реализации

## Important
- @docs/system-context.md — текущая архитектура и состояние
- @docs/discovery/INDEX.md — реестр всех discovery-итераций
```

При установке плагина в целевой проект — CLAUDE.md проекта адаптируется, добавляя стек, команды сборки и конвенции.

### 0.2 Хуки

`hooks/hooks.json` — хуки плагина (автоматически применяются при установке):

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

Авто-линт добавляется после выбора языка целевого проекта.

---

## Фаза 1: Ideation Skill

### Назначение

Упаковка сырой идеи / разговора / описания в структурированный **Idea Brief** — максимум 100 строк. Это НЕ PRD, это «салфетка с чертежом», которая формализует What и Why, не трогая How.

### Skill: `skills/ideation/SKILL.md`

```yaml
---
name: ideation
description: >
  Structure a raw idea into a concise Idea Brief (<100 lines).
  Use when the user describes a new product, feature, or system
  and needs it formalized before writing a PRD.
  Triggers: "ideate", "idea brief", "формализовать идею", "упаковать идею"
allowed-tools: Read, Grep, Glob, Write
---
```

**Содержимое SKILL.md — инструкции:**

1. Прочитай `@docs/system-context.md` если существует (понять что уже есть)
2. Прочитай `@docs/discovery/INDEX.md` если существует (определить следующий номер итерации)
3. Задай до 5 уточняющих вопросов в стиле Mom Test (что решаем, для кого, какие ограничения)
4. Сгенерируй Idea Brief по шаблону [шаблону](templates/idea-brief.md)
5. Определи следующий номер итерации (NN) из `docs/discovery/INDEX.md` или начни с 01
6. Создай папку `docs/discovery/NN-<slug>/` и сохрани туда `idea-brief.md`
7. Обнови `docs/discovery/INDEX.md` — добавь новую строку со статусом `Discovery`

### Шаблон: `skills/ideation/templates/idea-brief.md`

```markdown
# Idea Brief: [Название]

## Problem Statement
[2-3 предложения: какую боль решаем, для кого]

## Proposed Solution
[3-5 предложений: что делаем на high level]

## Key User Flows
1. [Основной flow]
2. [Второй flow если есть]

## Scope Boundaries
- IN: [что входит]
- OUT: [что явно не входит в MVP]

## Success Criteria
- [Метрика 1]
- [Метрика 2]

## Known Constraints
- [Технические]
- [Бизнесовые]
- [Временные]

## Open Questions
- [Что ещё нужно выяснить]
```

### Command: `commands/ideate.md`

```markdown
Структурируй идею в Idea Brief.

Используй навык ideation. Входные данные: $ARGUMENTS

Если $ARGUMENTS пустые — спроси пользователя описать идею.
Если есть @docs/system-context.md — учти текущее состояние системы.
```

---

## Фаза 2: PRD Skill

### Назначение

Генерация полноценного PRD из Idea Brief + System Context. Обязательные секции: функциональные требования, API-контракты, data model, тестирование.

### Ключевая механика: учёт существующей системы

PRD-скилл **всегда** начинает с чтения `@docs/system-context.md`. Это решает проблему «чистого листа»:
- **Первый PRD** (system-context пустой): генерирует с нуля, в секции "Current System State" пишет "Greenfield"
- **Последующие PRD**: читает существующие API, модели, сервисы и строит delta — что добавляем/меняем

### Skill: `skills/prd/SKILL.md`

```yaml
---
name: prd
description: >
  Generate a Product Requirements Document from an Idea Brief.
  Always reads system-context.md to account for existing system state.
  Includes functional requirements, API contracts, data models, and test strategy.
  Triggers: "prd", "requirements", "написать PRD", "продуктовые требования"
allowed-tools: Read, Grep, Glob, Write
---
```

**Инструкции:**

1. Прочитай `@docs/system-context.md` — это текущее состояние системы
2. Прочитай Idea Brief из `docs/discovery/NN-<slug>/idea-brief.md` (передаётся как аргумент или выбирается)
3. Если system-context не пустой — определи что уже реализовано и строй PRD как delta
4. Сгенерируй PRD по шаблону [шаблону](templates/prd.md)
5. Для каждого API endpoint — укажи метод, path, request/response schema
6. Для каждой модели данных — укажи поля, типы, constraints
7. Секция Testing Strategy — обязательна, включает подход и ключевые сценарии
8. Сохрани в `docs/discovery/NN-<slug>/prd.md` (в ту же папку что и idea-brief)
9. Обнови статус в `docs/discovery/INDEX.md` на `In Review`

### Шаблон: `skills/prd/templates/prd.md`

```markdown
# PRD: [Feature Name]

**Status:** Draft | Review | Approved
**Date:** YYYY-MM-DD
**Idea Brief:** [ссылка на idea brief]

## Current System State
[Автоматически заполняется из system-context.md]
[Что уже существует и как новая фича к этому относится]
[Если greenfield — указать явно]

## Overview
[Что делаем и зачем — 3-5 предложений]

## Functional Requirements

### FR-1: [Название]
- **Description:** [что должна делать система]
- **Priority:** P0 | P1 | P2
- **Acceptance Criteria:**
  - [ ] [Критерий 1]
  - [ ] [Критерий 2]

### FR-2: [Название]
...

## API Contracts

### [POST /api/v1/resource]
- **Description:** [назначение]
- **Auth:** [требования]
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
- **Error Codes:** 400, 401, 404, 500 — [когда каждый]

## Data Models

### [ModelName]
| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | UUID | yes | Primary key |
| ... | ... | ... | ... |

**Indexes:** [список индексов]
**Relations:** [связи с другими моделями]

## System Changes
[Что меняется в существующей системе]
- New services: [список]
- Modified services: [список с описанием изменений]
- New infrastructure: [что нужно создать]
- Modified infrastructure: [что нужно изменить]

## Non-Functional Requirements
- **Performance:** [целевые метрики]
- **Scalability:** [ожидаемая нагрузка]
- **Security:** [требования]
- **Observability:** [логирование, мониторинг, алерты]

## Testing Strategy
- **Unit tests:** [что покрываем, целевой coverage]
- **Integration tests:** [ключевые сценарии]
- **E2E scenarios:** [критический путь]

## Dependencies & Risks
| Risk | Impact | Mitigation |
|------|--------|------------|
| ... | ... | ... |

## Out of Scope
[Что явно не делаем в рамках этого PRD]
```

---

## Фаза 3: Task Decomposition Skill

### Назначение

Нарезка PRD на конкретные задачи для разработчиков. Каждая задача — атомарная, с acceptance criteria, зависимостями и оценкой сложности.

### Skill: `skills/task-decomposition/SKILL.md`

```yaml
---
name: task-decomposition
description: >
  Break down a PRD into developer tasks with dependencies, acceptance criteria,
  and role assignments. Generates ordered task list ready for sprint planning.
  Triggers: "decompose", "нарезать задачи", "task breakdown", "разбить на задачи"
allowed-tools: Read, Grep, Glob, Write
---
```

**Инструкции:**

1. Прочитай PRD из `docs/discovery/NN-<slug>/prd.md` (передаётся как аргумент или выбирается)
2. Прочитай `@docs/system-context.md` для понимания существующего кода
3. Разбей на задачи, группируя по:
   - **Infrastructure** (IaC, CI/CD, environments)
   - **Backend** (API, бизнес-логика, интеграции)
   - **Data** (модели, миграции, индексы)
   - **Frontend** (UI, если применимо)
4. Для каждой задачи заполни шаблон [шаблону](templates/task.md)
5. Определи зависимости между задачами (какая задача блокирует какую)
6. Выстрой последовательность выполнения
7. Сохрани в `docs/discovery/NN-<slug>/tasks/` (INDEX.md + отдельные TASK-NNN.md)
8. Обнови статус в `docs/discovery/INDEX.md` на `Approved`

### Шаблон: `skills/task-decomposition/templates/task.md`

```markdown
# TASK-[NNN]: [Название]

**Role:** Backend | Frontend | Infrastructure | Data
**Priority:** P0 | P1 | P2
**Complexity:** S | M | L | XL
**Depends on:** TASK-XXX, TASK-YYY (или "none")
**PRD Reference:** FR-N [ссылка на требование]

## Description
[Что нужно сделать — конкретно, без абстракций]

## Acceptance Criteria
- [ ] [Конкретный проверяемый критерий]
- [ ] [Ещё один]
- [ ] Tests written and passing

## Technical Notes
[Подсказки по реализации, рекомендуемые подходы, файлы для изменения]

## Files to Create/Modify
- `path/to/new/file.py` — [назначение]
- `path/to/existing/file.py` — [что меняем]
```

### Шаблон: `skills/prd/templates/discovery-index.md`

```markdown
# Discovery Index

**Project:** [Название проекта]
**Last updated:** YYYY-MM-DD

| # | Slug | Status | Date Started | Summary |
|---|------|--------|--------------|---------|
| 01 | example-feature | Done | YYYY-MM-DD | Краткое описание итерации |
```

Статусы: `Discovery` → `In Review` → `Approved` → `Done`

### Выходной артефакт: Task Index

Помимо отдельных файлов задач — генерируется сводный `docs/discovery/NN-<slug>/tasks/INDEX.md`:

```markdown
# Tasks: [Feature Name]
**PRD:** [ссылка]
**Total:** N tasks (X backend, Y infra, Z data)

## Execution Order

### Wave 1 (параллельно, нет зависимостей)
- [ ] TASK-001: Setup OpenSearch index schema [Data, S]
- [ ] TASK-002: Create Lambda skeleton + IAM roles [Infra, M]

### Wave 2 (зависит от Wave 1)
- [ ] TASK-003: Implement URL parser service [Backend, M] → depends: TASK-002
- [ ] TASK-004: Implement web scraper [Backend, L] → depends: TASK-002

### Wave 3
- [ ] TASK-005: Integration: parser → scraper → storage [Backend, L] → depends: TASK-003, TASK-004
...
```

---

## Фаза 4: Замыкание цикла — Update Context

### Назначение

После реализации фичи разработчик запускает `/update-context` — скилл читает текущий код, PRD и обновляет `docs/system-context.md`.

### Skill: `skills/update-context/SKILL.md`

```yaml
---
name: update-context
description: >
  Update system-context.md after a feature is implemented.
  Scans codebase and PRD to reflect current system state.
  Triggers: "update context", "обновить контекст", after feature completion
allowed-tools: Read, Grep, Glob, Write, Bash
---
```

**Инструкции:**

1. Прочитай текущий `@docs/system-context.md`
2. Определи что изменилось (новые файлы, API, модели, сервисы)
3. Прочитай последний реализованный PRD из `docs/discovery/NN-<slug>/prd.md`
4. Обнови system-context.md, добавив:
   - Новые компоненты/сервисы
   - Новые API endpoints
   - Новые/изменённые модели данных
   - Инфраструктурные изменения
   - Ссылку на PRD как источник решения
5. НЕ удаляй существующую информацию, только добавляй/обновляй
6. Обнови статус итерации в `docs/discovery/INDEX.md` на `Done`

### Шаблон: `docs/system-context.md`

```markdown
# System Context

**Last updated:** YYYY-MM-DD
**Project:** [Название]

## Architecture Overview
[High-level описание системы — обновляется по мере роста]

## Components

### [Component Name]
- **Type:** Lambda | Service | Database | Queue | ...
- **Purpose:** [что делает]
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

## Порядок реализации

```
Milestone 1: Skeleton                          [Неделя 1, дни 1-2]
├── Создать plugin structure (.claude-plugin/, skills/, commands/, hooks/)
├── Написать plugin.json manifest
├── Написать CLAUDE.md (пример для проекта-потребителя)
├── Создать шаблоны внутри skills/*/templates/
└── Написать README.md с инструкцией по установке

Milestone 2: Ideation Skill                    [Неделя 1, дни 2-3]
├── Написать skills/ideation/SKILL.md
├── Написать commands/ideate.md
├── Протестировать: claude --plugin-dir ./ + /sdlc-toolkit:ideate
├── Проверить output на AI Newsletter идее
└── Итерировать описание скилла по качеству output

Milestone 3: PRD Skill                         [Неделя 1, дни 3-5]
├── Написать skills/prd/SKILL.md
├── Написать commands/prd.md
├── Протестировать: /sdlc-toolkit:prd для Step Function 1 (Article Processing)
├── Проверить: API контракты конкретные? Acceptance criteria проверяемые?
├── Проверить: discovery папка создаётся автоматически?
└── Итерировать

Milestone 4: Task Decomposition Skill          [Неделя 2, дни 1-2]
├── Написать skills/task-decomposition/SKILL.md
├── Написать commands/decompose.md
├── Протестировать: /sdlc-toolkit:decompose для PRD Article Processing
├── Проверить: есть зависимости? acceptance criteria? atomic?
└── Итерировать

Milestone 5: Update Context Skill              [Неделя 2, день 3]
├── Написать skills/update-context/SKILL.md
├── Написать commands/update-context.md
├── Имитировать реализацию SF1 → /sdlc-toolkit:update-context
├── Проверить: статус в discovery/INDEX.md обновился на Done?
└── Проверить: следующий /sdlc-toolkit:prd видит что SF1 уже реализован?

Milestone 6: Full Cycle Validation             [Неделя 2, дни 4-5]
├── Прогнать полный цикл для Step Function 2 (AI Enrichment):
│   ├── /sdlc-toolkit:ideate → docs/discovery/02-ai-enrichment/idea-brief.md
│   ├── /sdlc-toolkit:prd → docs/discovery/02-ai-enrichment/prd.md
│   ├── /sdlc-toolkit:decompose → docs/discovery/02-ai-enrichment/tasks/
│   └── /sdlc-toolkit:update-context → обновлённый system-context + INDEX.md
├── Документировать pain points и gaps
├── Обновить README с lessons learned
└── Создать issues для будущих скиллов (review, testing, SA)
```

---

## Будущие фазы (после MVP)

| Фаза | Скилл/Артефакт | Приоритет |
|------|----------------|-----------|
| Code Review | Субагент `reviewer.md` + хук PostToolUse | Высокий |
| Testing | TDD-скилл + авто-тест хук | Высокий |
| Architecture | Субагент `architect.md` (read-only, Opus) | Средний |
| Business Requirements | Скилл BR между ideation и PRD | Средний |
| Solution Architecture | SA-скилл между PRD и tasks | Средний |
| Sprint Planning | Группировка задач в спринты | Низкий |
| Retrospective | Скилл анализа что пошло не так | Низкий |
| Onboarding | Команда `/onboard` для новых в команде | Низкий |

---

## Критерии успеха MVP

1. **Полный цикл проходит за <1 час** для одной фичи (ideation → tasks)
2. **Второй PRD учитывает первый** — system-context корректно отражает реализованное
3. **Задачи атомарные** — разработчик берёт задачу и не задаёт вопросов «а что имеется в виду?»
4. **Артефакты в git** — всё версионировано, ревьюабельно, трейсабельно
5. **Переносимость** — тулкит можно скопировать в другой проект и адаптировать за 30 минут