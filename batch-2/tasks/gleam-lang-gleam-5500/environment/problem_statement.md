# Formatter Moves Comments Out of Type Annotations

## Description

The Gleam code formatter incorrectly handles comments that appear within type annotations. When a developer adds comments before individual type arguments inside a function type annotation — for example, to document what each parameter represents — running the formatter either drops those comments or moves them to an incorrect position. The same issue occurs with comments placed before elements in tuple return type annotations: a comment intended to appear before a specific element ends up displaced to after that element.

## Expected Behavior

- Comments placed before a type argument within a function type annotation should remain before that argument after formatting.
- Comments before elements in a tuple type annotation should appear before the element they describe, not after it.
- Trailing comments at the end of a function type's argument list should be preserved in place.
- Nested function types with commented type arguments should format with all comments in their correct positions.
- Multiple consecutive comment lines before a single type argument should all be preserved together.

## Why This Matters

Developers sometimes add explanatory comments within complex function type signatures to describe what each parameter is. When the formatter destroys or misplaces these comments, it discourages inline documentation within types and can silently corrupt carefully authored code every time the formatter runs. The formatter should be idempotent — running it multiple times on already-formatted code should produce no changes.
