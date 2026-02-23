---
name: update-context
description: >
  Update system-context.md after a feature is implemented.
  Scans codebase and PRD to reflect current system state.
  Triggers: "update context", "обновить контекст", after feature completion
allowed-tools: Read, Grep, Glob, Write, Bash
---

# Update Context Skill

Обнови system-context.md после реализации фичи.
Скилл сканирует кодовую базу и PRD, отражая текущее состояние системы.

## Инструкции

1. Прочитай текущий `@docs/system-context.md`
2. Определи что изменилось:
   - Просканируй кодовую базу на новые файлы, сервисы, API endpoints
   - Проверь новые/изменённые модели данных
   - Проверь инфраструктурные изменения (IaC, конфиги)
3. Прочитай последний реализованный PRD из `docs/discovery/NN-<slug>/prd.md`
4. Если `docs/system-context.md` не существует — создай его по [шаблону](templates/system-context.md)
5. Обнови `docs/system-context.md`, добавив:
   - Новые компоненты/сервисы в секцию Components
   - Новые API endpoints в таблицу API Endpoints
   - Новые/изменённые модели данных в секцию Data Models
   - Инфраструктурные изменения в секцию Infrastructure
   - Новую строку в таблицу Implemented Features
   - Ключевые решения в таблицу Key Decisions
   - Ссылку на PRD как источник решения
6. **НЕ удаляй** существующую информацию — только добавляй/обновляй
7. Обнови дату "Last updated" в system-context.md
8. Обнови статус итерации в `docs/discovery/INDEX.md` на `Done`

## Важно

- system-context.md — это **append-only** документ (информация добавляется, не удаляется)
- Каждое изменение должно содержать ссылку на PRD-источник ("Added in: PRD-slug")
- После обновления следующий `/prd` должен корректно видеть что уже реализовано
- Architecture Overview обновляется инкрементально — добавляй новые компоненты, не переписывай всё

## Supporting files

- [templates/system-context.md](templates/system-context.md) — начальный шаблон system-context
