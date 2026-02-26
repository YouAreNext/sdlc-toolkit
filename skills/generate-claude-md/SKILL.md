---
name: generate-claude-md
description: >
  Generate a high-quality CLAUDE.md file for any project.
  Analyzes project structure, asks clarifying questions, and produces
  a concise CLAUDE.md following proven best practices (WHAT/WHY/HOW framework,
  under 80 lines, progressive disclosure, MUST/NEVER language).
  Also generates .claude/rules/ for path-scoped conventions.
  Triggers: "generate claude.md", "create claude.md", "init claude.md",
  "bootstrap project"
allowed-tools: Read, Grep, Glob, Bash, Write
---

# Generate CLAUDE.md Skill

Generate a production-quality CLAUDE.md for a project, following best practices.
The output must be concise, universally applicable, and use progressive disclosure.

## Critical Constraints

Claude Code's system prompt already uses ~50 instructions. Frontier models reliably
follow ~150-200 total. This leaves ~100-150 for ALL CLAUDE.md files + skills + rules.

Therefore:
- Root CLAUDE.md MUST be **under 80 lines** (ideally 40-60)
- Every line MUST be **universally applicable** to all tasks in this project
- Task-specific instructions go into `.claude/rules/` with path scoping
- Detailed docs go into `docs/` referenced via `@` imports

## Process

### Step 1: Analyze Project

Scan the project to detect:

1. Read `package.json` / `requirements.txt` / `pyproject.toml` / `go.mod` / `Cargo.toml`
   → Determine language, framework, dependencies
2. Read existing config files:
   - `.eslintrc` / `ruff.toml` / `biome.json` → Linter exists? (don't add style rules)
   - `tsconfig.json` / `mypy.ini` → Type checker config
   - `jest.config` / `pytest.ini` / `vitest.config` → Test framework
   - `docker-compose.yml` / `Dockerfile` → Infrastructure
   - `.github/workflows/` → CI/CD
3. Scan directory structure:
   - Glob `src/**/` → Identify architecture layers
   - Check for monorepo patterns (`apps/`, `packages/`, `services/`)
4. Read existing `README.md` if present → Project purpose
5. Read existing `CLAUDE.md` if present → What to preserve/improve

### Step 2: Ask Clarifying Questions (max 5)

Ask ONLY what cannot be inferred from code. Use targeted questions:

1. **Purpose**: "What does this project do in one sentence?" (skip if README is clear)
2. **Architecture decisions**: "Any non-obvious architectural choices I should know?"
3. **Key constraints**: "Any MUST/MUST NOT rules the team follows?"
4. **Workflow preferences**: "Should Claude propose plans before coding, or go direct?"
5. **What's special**: "Anything unusual about this project that would surprise a new developer?"

Skip questions where the answer is obvious from code analysis.

### Step 3: Generate CLAUDE.md

Follow the **WHAT / WHY / HOW** framework. Use the [CLAUDE.md template](templates/claude-md.md).

**Generation rules:**
- MUST be **under 80 lines**
- MUST NOT include code style rules if a linter is configured
- MUST NOT include generic programming advice ("write clean code", "handle errors")
- MUST NOT include task-specific instructions (use rules/ for those)
- MUST use **MUST / MUST NOT / NEVER** for hard constraints (not "prefer" or "try to")
- MUST include exact, copy-paste-ready commands for build/test/lint
- MUST use `@path/to/doc.md` for detailed docs instead of inlining
- MUST NOT copy code snippets — use file:line references instead
- Prefer pointing Claude to WHERE info is, not duplicating it

### Step 4: Generate Scoped Rules (if applicable)

If the project has distinct domains (API, frontend, infra), create scoped rules in `.claude/rules/`.

Use the [scoped rule template](templates/scoped-rule.md).

Each rule file:
- MUST have `paths:` frontmatter with glob patterns
- MUST be under 30 lines
- MUST contain only instructions relevant to those file paths

### Step 5: Validate

Run validation checklist:

- [ ] Total lines ≤ 80?
- [ ] No code style rules? (check: linter configured?)
- [ ] No generic advice? ("write clean code", "handle errors")
- [ ] No task-specific instructions? (belongs in rules/ or skills/)
- [ ] All commands are exact and runnable?
- [ ] Hard constraints use MUST/MUST NOT/NEVER?
- [ ] Detailed docs referenced via @ imports, not inlined?
- [ ] No copied code snippets? (use file:line refs)
- [ ] Would every instruction be useful for EVERY task in this project?

If validation fails, revise before saving.

### Step 6: Save

- Save `CLAUDE.md` to project root
- Save `.claude/rules/*.md` if generated
- If `CLAUDE.local.md` doesn't exist, create a minimal one from the [local template](templates/claude-local-md.md)
- Report: line count, rules count, what was included/excluded and why

## Anti-patterns to Avoid

**Never include in CLAUDE.md:**

- Generic advice (Claude already knows): "Write clean code", "Handle errors properly", "Follow SOLID"
- Code style rules (use linter + hook instead): indentation, quotes, trailing commas, line length
- Exhaustive docs (use @import): listing all API endpoints, all models, all components inline
- Task-specific instructions (use rules/): "When writing migrations, always...", "When creating React components..."
- Hotfix log (group and generalize): "NEVER use setTimeout for async (added 01/15 after bug)"

## Supporting files

- [templates/claude-md.md](templates/claude-md.md) — CLAUDE.md template
- [templates/claude-local-md.md](templates/claude-local-md.md) — CLAUDE.local.md template
- [templates/scoped-rule.md](templates/scoped-rule.md) — Scoped rule template
