---
description: Establish technical foundation for a new project
---

Establish technical foundation for a new project.

Use the foundation skill. Input data: $ARGUMENTS

$ARGUMENTS can be:
- Language/framework (e.g.: "fastapi", "django", "express") — start with this stack
- Empty — ask the user what language/framework they're using

**What this skill does:**
1. Asks about language/framework and project scale
2. Proactively SUGGESTS best practices for the chosen stack (searches for current patterns)
3. Shows concrete folder structure options (MVP vs Scalable)
4. Walks through technical decisions: database, ORM, auth, testing, deployment
5. Creates `.claude/rules/tech-stack.md` with agreed conventions
6. Updates `docs/system-context.md` with Tech Stack section

**When to use:**
- Starting a new (greenfield) project
- Adding a major new component
- PRD skill will suggest this automatically if system-context is empty

**Output:**
- `.claude/rules/tech-stack.md` — rules for Claude (auto-loaded)
- `docs/system-context.md` — updated with Tech Stack
