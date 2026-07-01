# CLI Extension Init Generates Stale SDK Version Reference

## Description

When a developer uses the CLI to scaffold a new extension project (resolver or authentication type), the generated project template embeds a hard-coded reference to a specific version of the SDK library. That embedded version has fallen behind the current SDK release, meaning all newly created extension projects start off referencing an outdated version of the library.

At the same time, the SDK itself has a compilation issue that needs to be fixed: the current codebase does not compile cleanly, which blocks any work on extension-related features.

## Expected Behavior

- Initializing a new resolver extension should generate a project that references the current SDK version in both its regular and dev dependencies.
- Initializing a new authentication extension should generate a project that references the current SDK version in both its regular and dev dependencies.
- The entire codebase, including the SDK and all packages that depend on it, should compile without errors after the changes are applied.

## Why This Matters

Developers who scaffold new extension projects using the CLI immediately get outdated dependencies, which can lead to subtle incompatibilities or the need to manually update the generated files right after creation. Fixing the embedded version reference and resolving the underlying compilation issue together ensures a smooth developer experience when starting new extension projects.
