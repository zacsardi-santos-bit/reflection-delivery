## Description

The import organization feature in Biome does a great job of sorting and grouping imports and re-exports that reference a source module. However, it currently does not fully handle "bare" exports — those that export identifiers from the current file without naming any source module.

When a file has multiple consecutive bare exports, they are left as separate statements without being merged or sorted. When bare exports appear alongside import statements, no blank-line separation is applied between them. Additionally, custom group ordering configurations (such as placing type-only exports before value exports, or treating Node.js built-in module exports differently) are not applied to bare exports.

## Expected Behavior

- When a file has both imports and bare exports, a blank line should be inserted between them to separate them into distinct groups.
- Multiple consecutive bare export statements appearing in the same group should be merged into a single export statement, with their specifiers sorted alphabetically.
- Custom group ordering options should apply to export statements, including type-only exports and exports from Node.js built-in modules.
- Bare exports that are separated from other exports by non-export code (such as function declarations) should remain in their own separate group.

## Why This Matters

Files that mix imports with bare exports, or that have multiple bare export statements, end up with inconsistently organized code even after running the organize imports action. Developers expect the action to handle all export and import statements uniformly so the file is fully organized in one pass.
