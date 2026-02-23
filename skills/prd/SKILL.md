---
name: prd
description: >
  Generate a Product Requirements Document from an Idea Brief or raw description.
  Always reads system-context.md to account for existing system state.
  Includes functional requirements, API contracts, data models, and test strategy.
  Triggers: "prd", "requirements", "написать PRD", "продуктовые требования"
allowed-tools: Read, Grep, Glob, Write
---

# PRD Skill

Сгенерируй полноценный PRD на основе Idea Brief, описания от пользователя или System Context.
PRD должен содержать: функциональные требования, API-контракты, data models, стратегию тестирования.

## Точки входа

PRD можно создать из разных источников:
- **Из Idea Brief** — если фаза ideation уже пройдена, читай `docs/discovery/NN-<slug>/idea-brief.md`
- **Напрямую** — если ideation пропущена, PRD создаётся из описания пользователя или аргументов команды

## Ключевая механика: учёт существующей системы

PRD-скилл **всегда** начинает с чтения system-context.md:
- **Первый PRD** (system-context пустой): генерирует с нуля, в секции "Current System State" пишет "Greenfield"
- **Последующие PRD**: читает существующие API, модели, сервисы и строит **delta** — что добавляем/меняем

## Инструкции

1. Прочитай `@docs/system-context.md` — это текущее состояние системы
2. Прочитай `@docs/discovery/INDEX.md` — определи следующий номер итерации
3. Определи источник требований:
   - Если передан путь к idea-brief или slug итерации — прочитай Idea Brief из `docs/discovery/NN-<slug>/idea-brief.md`
   - Если передано текстовое описание — используй его как входные данные напрямую
   - Если аргументы пустые — спроси пользователя что делаем
4. Если папка `docs/discovery/NN-<slug>/` ещё не существует — создай её (определи следующий NN из INDEX.md или начни с 01)
5. Если `docs/discovery/INDEX.md` не существует — создай его по [шаблону](templates/discovery-index.md)
6. Если system-context не пустой — определи что уже реализовано и строй PRD как delta
7. Сгенерируй PRD по [шаблону](templates/prd.md)
8. Для каждого API endpoint — укажи метод, path, request/response schema в JSON
9. Для каждой модели данных — укажи поля, типы, constraints, индексы
10. Секция Testing Strategy — обязательна, включает подход и ключевые сценарии
11. Сохрани в `docs/discovery/NN-<slug>/prd.md`
12. Обнови `docs/discovery/INDEX.md` — добавь или обнови строку, статус `In Review`

## Важно

- **Каждый** API endpoint должен иметь конкретный request/response schema — не абстракции
- **Каждое** функциональное требование должно иметь проверяемые acceptance criteria
- Секция "Current System State" заполняется автоматически из system-context.md
- Если greenfield — укажи явно
- Data Models должны включать типы полей, constraints и связи между моделями
- Slug для папки — короткий, через дефис, на английском (например: `article-processing`, `user-auth`)

## Supporting files

- [templates/prd.md](templates/prd.md) — шаблон PRD
- [templates/discovery-index.md](templates/discovery-index.md) — шаблон Discovery Index
