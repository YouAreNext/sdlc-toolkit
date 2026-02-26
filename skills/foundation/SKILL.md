---
name: foundation
description: >
  Establish technical foundation for a new project.
  Proactively suggests stack-appropriate decisions including project structure.
  Creates .claude/rules/tech-stack.md with project conventions.
  Triggers: "foundation", "tech stack", "setup project", "project structure"
allowed-tools: Read, Grep, Glob, Write
---

# Foundation Skill

Establish technical foundation before writing PRDs. Proactively suggests best practices for the chosen stack.

> **When to use:** Greenfield projects, or when starting a major new component.
> PRD skill will suggest this automatically if system-context is empty and no tech-stack.md exists.

## Output

- `.claude/rules/tech-stack.md` — rules for Claude to follow (auto-loaded)
- `docs/system-context.md` — updated with Tech Stack section

ADRs are created separately via `/sdlc-toolkit:adr` if the user wants detailed documentation.

## Instructions

### Phase 1: Language & Scale

1. Check if `.claude/rules/tech-stack.md` already exists — if yes, ask if user wants to update or skip
2. Read `docs/system-context.md` if it exists — understand current state
3. Ask the user:
   - **What language/framework?** (e.g., FastAPI, Django, Express, Go, Spring Boot)
   - **Estimated scale?** How many endpoints/features are planned?
     - `< 5 endpoints` → suggest **MVP structure** (simple, flat)
     - `> 5 endpoints` → suggest **Scalable structure** (DDD-lite, bounded contexts)

### Phase 2: Project Structure (CRITICAL)

4. Based on language + scale, show the CONCRETE folder structure from [templates/project-structures.md](templates/project-structures.md)
5. Explain WHY this structure is recommended:
   - Separation of concerns
   - Testability (tests next to code)
   - Scalability path
6. **IMPORTANT:** Explain the "tests next to code" rule:
   - Unit tests live INSIDE each module: `users/tests/test_service.py`
   - Integration/E2E tests live in root `tests/` folder
   - NEVER dump all tests into a single `tests/` folder
7. Confirm or adjust the structure with the user

### Phase 3: Tech Stack Decisions

8. Based on the chosen language/framework, **PROACTIVELY SUGGEST** a complete stack.
   Use [templates/tech-stack-presets.md](templates/tech-stack-presets.md) as reference:

   | Stack | Suggested Preset |
   |-------|------------------|
   | FastAPI | SQLModel, pytest-asyncio, Pydantic v2, Alembic |
   | Django | Django ORM, pytest-django, DRF |
   | Express | Prisma or TypeORM, Jest, Zod |
   | Go | sqlc or GORM, go test, chi/gin |
   | Spring Boot | JPA/Hibernate, JUnit 5, Flyway |

9. Walk through each category, confirming or adjusting:
   - **Database** — type, dev/prod differences
   - **ORM/Data Access** — ORM vs raw SQL, migrations
   - **API Style** — REST/GraphQL, versioning
   - **Authentication** — strategy (JWT, sessions, OAuth)
   - **Testing** — framework, coverage targets
   - **Deployment** — target environment (Docker, serverless, etc.)

### Phase 4: Generate Output

10. Create `.claude/rules/tech-stack.md` using [templates/tech-stack-rules.md](templates/tech-stack-rules.md)
    - Include ALL decided rules
    - Include project structure rules
    - Include test location rules
11. Update `docs/system-context.md`:
    - Add "Tech Stack" section at the top
    - If file doesn't exist, create it with Tech Stack section
12. Offer to create ADRs for major decisions via `/sdlc-toolkit:adr`

## Important

- **Be proactive:** Don't just ask questions — SUGGEST solutions based on the stack
- **Show concrete examples:** Always show actual folder structures, not abstract descriptions
- **Explain trade-offs:** Help user understand MVP vs Scalable choice
- **Focus on rules that matter:** Only include rules that affect how Claude writes code
- The goal is to prevent technical debt by making decisions explicit BEFORE development starts

## Supporting files

- [templates/tech-stack-rules.md](templates/tech-stack-rules.md) — Template for .claude/rules/
- [templates/tech-stack-presets.md](templates/tech-stack-presets.md) — Presets for popular stacks
- [templates/project-structures.md](templates/project-structures.md) — MVP and Scalable folder structures
