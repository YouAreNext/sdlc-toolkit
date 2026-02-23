# SDLC Toolkit

Модульный набор Claude Code skills для полного цикла разработки.
Установлен как плагин: skills доступны через namespace `sdlc-toolkit:`.

## Workflow
[Ideation] → PRD → Task Decomposition → Development → Update Context → (repeat)

Ideation — опциональная фаза. Можно начать итерацию сразу с /sdlc-toolkit:prd.

## Conventions
- Все output-артефакты — markdown в docs/ или рядом с кодом
- system-context.md — single source of truth о текущем состоянии проекта
- Каждая итерация живёт в отдельной папке docs/discovery/NN-<slug>/
- Discovery-артефакты (idea brief, PRD, tasks, decisions) группируются по итерации
- Финальные артефакты после апрува обновляют системные docs (docs/api/, docs/models/) через /sdlc-toolkit:update-context
- docs/discovery/INDEX.md — реестр всех итераций со статусами
- PRD всегда читает @docs/system-context.md перед генерацией
- Задачи содержат acceptance criteria и зависимости
- После реализации фичи — обязательный /sdlc-toolkit:update-context

## Skills
- /sdlc-toolkit:ideate — (опционально) упаковка идеи в idea brief (<100 строк)
- /sdlc-toolkit:prd — генерация PRD из idea brief, описания или system context
- /sdlc-toolkit:decompose — нарезка PRD на задачи по ролям
- /sdlc-toolkit:update-context — обновление system-context.md после реализации

## Important
- @docs/system-context.md — текущая архитектура и состояние
- @docs/discovery/INDEX.md — реестр всех discovery-итераций
