Generate a PRD (Product Requirements Document).

Use the prd skill. Input data: $ARGUMENTS

$ARGUMENTS can be:
- Path to idea-brief.md or iteration slug (e.g.: "01-article-processing") — PRD is built based on the Idea Brief
- Text description of the feature — PRD is created directly, without a preliminary Idea Brief
- Empty — ask the user what we're building

If the discovery iteration folder doesn't exist yet — create it.
Always read @docs/system-context.md before generation — consider what's already implemented.
