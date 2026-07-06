## Description

pnpm generates an inconsistent lockfile when packages form a specific "diamond" peer dependency shape. The issue occurs when a plugin depends on both a parser and a shared runtime, while the parser also depends on that same shared runtime. pnpm incorrectly pairs the plugin with a top-level parser instance that resolved the shared runtime at a different version than the one the plugin itself uses.

## Expected Behavior

- When a plugin peer-depends on both a parser and a shared runtime, and the parser also peer-depends on that shared runtime, all three must be resolved consistently: the plugin and its parser should use the same version of the shared runtime.
- If the top-level project has a newer version of the shared runtime, but the plugin is nested under a context that uses an older version, the plugin's parser should be the one from the older-version context — not the top-level parser instance.
- The lockfile should contain exactly one snapshot for the plugin that shows consistent peer resolution across all shared dependencies.

## Why This Matters

A real-world example of this pattern is a TypeScript toolchain where a linting plugin peer-depends on both a TypeScript parser and TypeScript itself, while the parser also peer-depends on TypeScript. If the root project uses TypeScript 2 but an inner package uses TypeScript 1, the plugin should always be paired with the TypeScript-1 version of the parser — but pnpm was instead reusing the hoisted TypeScript-2 parser for the plugin, causing a mismatch. This leads to incorrect dependency wiring and potential runtime errors in projects that use multiple TypeScript versions.
