## Description

A library that the Docker daemon uses for merging configuration structs and maps has moved to a new canonical module path. In its latest major version, the library author changed the import path from the old GitHub-hosted path to a new vanity URL. Any code that still imports using the old path will fail to build.

Currently, the moby codebase references the old import path in several places — production source files, test files, the module manifest, the checksum file, and the vendored source tree. This causes compilation failures.

## Expected Behavior

- All import references to the old module path must be updated to use the new canonical path.
- The module manifest and checksum file must reflect the new dependency.
- The vendored copy of the library must be relocated to match the new import path.
- All existing tests in the affected packages must continue to compile and pass after the migration.

## Why This Matters

Without this migration, any developer or CI pipeline that tries to build or test the daemon will encounter compilation errors. The library's API has not changed — this is purely an import path rename — so no functional behavior needs to change, only the references.
