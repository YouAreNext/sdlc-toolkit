---
name: ideation
description: >
  Structure a raw idea into a concise Idea Brief (<100 lines).
  Use when the user describes a new product, feature, or system
  and needs it formalized before writing a PRD.
  Triggers: "ideate", "idea brief", "формализовать идею", "упаковать идею"
allowed-tools: Read, Grep, Glob, Write
---

# Ideation Skill

> **Опциональная фаза.** Ideation полезна для сложных или размытых идей, но не обязательна.
> Если идея уже ясна — можно сразу переходить к `/prd`.

Упакуй сырую идею пользователя в структурированный Idea Brief — максимум 100 строк.
Idea Brief — это НЕ PRD. Это «салфетка с чертежом», которая формализует **What** и **Why**, не трогая How.

## Инструкции

1. Прочитай `@docs/system-context.md` если существует — пойми что уже есть в системе
2. Прочитай `@docs/discovery/INDEX.md` если существует — определи следующий номер итерации
3. Задай пользователю до 5 уточняющих вопросов в стиле Mom Test:
   - Какую проблему решаем? Кто с ней сталкивается?
   - Что сейчас делает пользователь вместо этого?
   - Какие ограничения (технические, временные, бюджетные)?
   - Что точно НЕ входит в scope?
   - Как поймём что получилось? (метрики успеха)
4. Сгенерируй Idea Brief по [шаблону](templates/idea-brief.md)
5. Определи следующий номер итерации (NN) из `docs/discovery/INDEX.md` или начни с 01
6. Создай папку `docs/discovery/NN-<slug>/` и сохрани туда `idea-brief.md`
7. Обнови `docs/discovery/INDEX.md` — добавь новую строку со статусом `Discovery`

## Важно

- Idea Brief должен быть **менее 100 строк**
- Не уходи в детали реализации — это задача PRD
- Если system-context существует, учитывай что уже реализовано
- Slug для папки — короткий, через дефис, на английском (например: `article-processing`, `user-auth`)

## Supporting files

- [templates/idea-brief.md](templates/idea-brief.md) — шаблон Idea Brief
