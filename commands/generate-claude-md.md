---
description: Generate a CLAUDE.md file for the current project
---

Generate a high-quality CLAUDE.md for this project.

Use the generate-claude-md skill. Input data: $ARGUMENTS

$ARGUMENTS can be:
- Empty — analyze current project and generate CLAUDE.md
- "update" — read existing CLAUDE.md and improve it
- "rules" — generate only .claude/rules/ scoped conventions

Analyze the project structure, dependencies, and configs first.
Ask clarifying questions only for things that cannot be inferred from code.
Output must be under 80 lines, no generic advice, no code style rules if linter exists.
