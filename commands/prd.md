---
description: Generate a PRD (Product Requirements Document)
---

Generate a PRD (Product Requirements Document).

Use the prd skill. Input data: $ARGUMENTS

$ARGUMENTS can be:
- Iteration slug (e.g.: "01-article-reading") — PRD is built based on existing Idea Brief in that folder
- Text description of the feature — PRD is created directly, new folder will be created
- Empty — ask the user what we're building

**Important workflow:**
1. If creating new iteration: generate slug from feature description (e.g., "User Auth" → `user-auth`)
2. Determine next iteration number (NN) from @docs/discovery/INDEX.md or start with 01
3. Create folder: `docs/discovery/NN-<slug>/` (e.g., `docs/discovery/01-user-auth/`)
4. Save prd.md INSIDE that folder

Always read @docs/system-context.md before generation — consider what's already implemented.
