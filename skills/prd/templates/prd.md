# PRD: [Feature Name]

**Status:** Draft | Review | Approved
**Date:** YYYY-MM-DD
**Idea Brief:** [link to idea brief]

## Current System State
[Auto-filled from system-context.md]
[What already exists and how the new feature relates to it]
[If greenfield — state explicitly]

## Overview
[What we're doing and why — 3-5 sentences]

## Functional Requirements

### FR-1: [Title]
- **Description:** [what the system should do]
- **Priority:** P0 | P1 | P2
- **Acceptance Criteria:**
  - [ ] [Criterion 1]
  - [ ] [Criterion 2]

### FR-2: [Title]
...

## API Contracts

### [POST /api/v1/resource]
- **Description:** [purpose]
- **Auth:** [requirements]
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
- **Error Codes:** 400, 401, 404, 500 — [when each occurs]

## Data Models

### [ModelName]
| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | UUID | yes | Primary key |
| ... | ... | ... | ... |

**Indexes:** [list of indexes]
**Relations:** [relationships with other models]

## System Changes
[What changes in the existing system]
- New services: [list]
- Modified services: [list with change descriptions]
- New infrastructure: [what needs to be created]
- Modified infrastructure: [what needs to be changed]

## Non-Functional Requirements
- **Performance:** [target metrics]
- **Scalability:** [expected load]
- **Security:** [requirements]
- **Observability:** [logging, monitoring, alerts]

## Testing Strategy
- **Unit tests:** [what we cover, target coverage]
- **Integration tests:** [key scenarios]
- **E2E scenarios:** [critical path]

## Dependencies & Risks
| Risk | Impact | Mitigation |
|------|--------|------------|
| ... | ... | ... |

## Out of Scope
[What we're explicitly not doing in this PRD]
