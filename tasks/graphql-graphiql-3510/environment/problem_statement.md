## Description

The GraphQL language server needs better handling for workspaces that don't have a valid configuration file. Currently, when a developer opens a workspace without a GraphQL config file (or with an empty one), the server silently fails to activate without giving a clear indication of why. There is also no reliable way to programmatically detect this "missing config" state from outside the processor.

Additionally, the logic for finding embedded GraphQL queries within JavaScript and TypeScript source files is currently embedded inside the main message processor, making it impossible to test in isolation and harder to reason about. This logic should be extracted into its own dedicated module so it can be independently tested and reused.

## Expected Behavior

- The language server must track and expose whether it has successfully initialized and whether a GraphQL config file is missing.
- When no valid config file is found, the server must log a clear error message indicating that the config file is not available in the provided config directory.
- The document parsing logic that finds embedded GraphQL queries in source files should live in its own module and be independently importable.
- When parsing source files, the function must gracefully handle edge cases: empty files, whitespace-only files, files with unsupported extensions, and files that cannot be parsed must all return no results without throwing errors.
- Files with content that produces no parseable nodes (such as files containing only comments) must also return empty results without crashing.

## Why This Matters

Developers troubleshooting why the GraphQL language server isn't working in their project need clear feedback when a configuration file is missing. Without this, it's very difficult to distinguish between a configuration problem and a bug in the server itself. Making the document parsing logic self-contained also improves the overall testability and maintainability of the codebase.
