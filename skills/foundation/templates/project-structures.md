# Project Structure Guidelines

Instructions for suggesting project structures. DO NOT use hardcoded templates — search for current best practices.

## How to Suggest Structure

1. **Ask about scale:**
   - `< 5 endpoints/features` → MVP (simple, flat)
   - `> 5 endpoints/features` → Scalable (modular, DDD-lite)

2. **Search for current best practices:**
   - Use web search: `"[framework] project structure 2024 best practices"`
   - Find at least 2 reputable sources (official docs, popular repos, well-known authors)
   - Prefer official documentation over blog posts

3. **Present options:**
   - Show MVP structure with rationale
   - Show Scalable structure with rationale
   - Explain when to choose which

## Critical Rule: Tests Next to Code

**ALWAYS enforce this rule regardless of framework:**

```
✅ CORRECT: Unit tests inside each module
users/
├── service.py
└── tests/
    └── test_service.py

❌ WRONG: All tests dumped together
tests/
├── test_users.py
├── test_items.py
└── test_orders.py
```

**Exception:** Go uses `_test.go` in same directory (idiomatic).

## What to Include in Suggestion

For each structure, explain:
- Entry point location
- Where business logic lives
- Where data access lives
- Where tests live (unit vs integration)
- Where shared/common code lives

## Sources to Search

- Official framework documentation
- GitHub trending repos with the framework
- "Awesome [framework]" lists
- Framework creator's recommendations

## Do NOT

- Use outdated patterns from old tutorials
- Copy structures without explaining rationale
- Ignore framework-specific idioms (e.g., Go's `_test.go`)
