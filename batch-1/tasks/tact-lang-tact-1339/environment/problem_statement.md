## Description

The Tact compiler's standard library content and path information are currently stored in a scattered manner. The prepackaged bundle of stdlib files lives under a dedicated imports directory, while individual files that need to locate stdlib content on disk each compute the path themselves using hardcoded relative navigation. This inconsistency makes it harder to maintain the codebase and means that any reorganization of stdlib files requires touching multiple unrelated parts of the compiler.

## Expected Behavior

- A dedicated module should exist that exports the bundled stdlib file contents (a map of relative file paths to file content), making it the single source of truth for the in-memory representation of stdlib.
- A separate dedicated module should export the canonical filesystem path to the stdlib root directory, so that any code needing to read stdlib files from disk can import this path rather than compute it inline.
- All existing compiler functionality (contract compilation, memory allocation resolution) should continue to work correctly when consuming stdlib data from these new locations.

## Why This Matters

Centralizing the stdlib bundle and its filesystem path into a dedicated module directory makes the compiler easier to maintain and refactor. It removes the need for consumers to independently know where the stdlib lives on disk, and ensures there is one authoritative place to update if the stdlib location ever changes.
