## Description

Vite's plugin system calls every plugin's hook handlers for every module it processes — even when a plugin only needs to act on a small subset of files. As a result, plugin authors must write repetitive filtering logic at the top of each handler (e.g., checking the file extension, ignoring virtual modules, etc.). This boilerplate is error-prone, makes plugins harder to read, and causes unnecessary work when a handler is invoked for files it would immediately bail on anyway.

## Feature Request

Add a built-in filter mechanism to plugin hooks so that a plugin can declare upfront which file IDs or code patterns a hook should apply to. When a filter is provided, Vite should skip calling the handler entirely for non-matching modules — the filter acts as a guard before the handler runs.

## Expected Behavior

- A plugin author should be able to attach a file-ID filter to the module resolution hook (matched by glob or regex pattern), so the handler is only called for matching files.
- A plugin author should be able to attach a file-ID filter to the module loading hook.
- A plugin author should be able to attach a file-ID filter and/or a code-content filter to the transformation hook. A code-content filter matches against the raw source code string of the module.
- When an ID filter explicitly includes a file (definite match), there is no need to also check the code filter — the handler runs regardless of code content.
- When an ID filter explicitly excludes a file, the handler is skipped regardless of code content.
- Exclude patterns should take higher priority than include patterns.
- Glob string patterns should match against paths relative to the project root, and should NOT match virtual module IDs (those starting with a null byte).
- Absolute paths should also be matchable by a relative glob pattern.
- These filters should behave identically in both the development server and the production build pipeline.

## Why This Matters

Without built-in filter support, every plugin that needs to target specific file types must reinvent the same filtering boilerplate. A declarative filter on the hook definition is cleaner, more performant (Vite can skip the handler call entirely), and consistent across the ecosystem.
