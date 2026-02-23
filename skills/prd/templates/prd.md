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
