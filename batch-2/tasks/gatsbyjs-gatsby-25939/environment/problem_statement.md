## Description

The Gatsby Recipes renderer produces plan output that is difficult to consume programmatically or display in non-terminal contexts. Two specific issues exist:

1. **Missing resource type information**: When the renderer produces a list of planned changes, each plan item is missing information about what kind of resource it represents. Tools or UI components that consume the plan output cannot determine whether a given item is a file operation, an npm package install, or another resource type without additional lookup.

2. **ANSI escape codes in diff output**: The diff text included with each plan item contains raw terminal color codes embedded in the string. This makes the diff output unreadable when displayed outside a terminal (e.g., in a web UI or when processed as plain text), and makes programmatic processing of diffs unnecessarily complex.

## Expected Behavior

- Each planned change item should include the name of the resource type it represents, available as a dedicated field on the plan object.
- The diff text for each plan item should be plain, readable text with no embedded terminal color or formatting codes.

## Why This Matters

Recipe plan output is consumed by both terminal UIs and other rendering surfaces. Having resource type information directly on plan items allows consumers to render appropriate labels without extra lookups. Having clean diff text ensures the output can be displayed, logged, or processed without stripping escape sequences first.
