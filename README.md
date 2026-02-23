# SDLC Toolkit

Claude Code плагин для полного MVP-цикла разработки: Ideation → PRD → Task Decomposition → Development → Context Updates.

## Installation

### Local testing
```bash
claude --plugin-dir ./sdlc-toolkit
```

### From marketplace
```bash
/plugin marketplace add <owner>/sdlc-toolkit
/plugin install sdlc-toolkit --scope project
```

### Manual copy
Скопируйте содержимое в директорию и подключите через `--plugin-dir`.

## Quick Start

1. Установите плагин одним из способов выше
2. Адаптируйте `CLAUDE.md` вашего проекта (добавьте стек, конвенции)
3. Запустите `/sdlc-toolkit:prd "описание фичи"` (или `/sdlc-toolkit:ideate` если идея сырая)

## Workflow

```
(опционально)
/sdlc-toolkit:ideate "описание идеи"
    ↓ docs/discovery/01-slug/idea-brief.md

/sdlc-toolkit:prd "описание фичи"          ← напрямую
/sdlc-toolkit:prd 01-article-processing     ← из Idea Brief
    ↓ docs/discovery/01-slug/prd.md

/sdlc-toolkit:decompose 01-article-processing
    ↓ docs/discovery/01-slug/tasks/

... разработка ...

/sdlc-toolkit:update-context
    ↓ docs/system-context.md (обновлён)
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
| `/sdlc-toolkit:ideate` | (опционально) Упаковка сырой идеи в Idea Brief (<100 строк) |
| `/sdlc-toolkit:prd` | Генерация PRD из Idea Brief, описания или System Context |
| `/sdlc-toolkit:decompose` | Нарезка PRD на задачи с зависимостями |
| `/sdlc-toolkit:update-context` | Обновление system-context.md после реализации |

## Discovery Structure

Каждая итерация живёт в отдельной папке `docs/discovery/NN-slug/` вашего проекта:

```
docs/discovery/01-article-processing/
├── idea-brief.md    # (опционально) что и зачем
├── prd.md           # Детальные требования
├── decisions.md     # ADR для этой итерации
└── tasks/           # Атомарные задачи
    ├── INDEX.md
    ├── TASK-001.md
    └── TASK-002.md
```

Статусы итераций: `Discovery` → `In Review` → `Approved` → `Done`

## Philosophy

Вместо хаотичного «напиши мне код» — структурированный процесс:
1. **Идея** (опционально) формализуется в Idea Brief
2. **Требования** детализируются в PRD с API-контрактами и моделями данных
3. **Задачи** нарезаются атомарно с зависимостями и acceptance criteria
4. **Контекст** обновляется после реализации — следующая итерация видит что уже сделано

## Adapting for Your Project

В `CLAUDE.md` вашего проекта добавьте:
- Стек проекта (язык, фреймворки, инфраструктура)
- Команды сборки и тестирования
- Конвенции именования и code style
- Ссылку на плагин: `sdlc-toolkit` установлен, команды через `/sdlc-toolkit:*`
