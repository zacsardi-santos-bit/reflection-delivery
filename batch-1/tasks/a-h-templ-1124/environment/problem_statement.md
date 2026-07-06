## Description

The language server has a mode intended for large codebases where files are not preloaded at startup. However, in this mode, when a user opens a template file, the language server has no mechanism to automatically discover and notify itself about all the related template files in the same package and its transitive dependencies. This means the language server is working with incomplete context.

Similarly, when the user closes a template file, the server has no way to know which dependency files can now also be safely closed (because no other open file still depends on them).

## Expected Behavior

- When a template file is opened, the server should automatically discover and open all template files across the full package dependency graph, loading dependencies before dependents (topological order).
- A reference count should be tracked per file so that files shared across multiple open packages are not opened multiple times by the language server.
- When a template file is closed, the server should decrement reference counts for all files in the dependency graph and only actually close files whose count reaches zero.
- Circular package import graphs must be handled without entering an infinite loop.

## Why This Matters

Without this lazy loading mechanism, the language server in no-preload mode is missing context about template files that the opened file transitively depends on. This leads to degraded language server functionality (missing completions, diagnostics, etc.) for users of large codebases who have opted out of startup preloading.
