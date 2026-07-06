## Description

Mocha's command-line interface uses a third-party argument parsing library to define and process all its CLI flags. The project is currently pinned to an older major version of that library. This older version exposed an import pattern (via a subpath) and an API method for resetting state that are no longer available in the current major version. As a result, attempting to use or test the CLI option setup with the updated library causes failures across the entire suite of CLI option tests.

## Expected Behavior

- The argument parsing library import in the CLI entry point should use the modern, top-level import pattern supported by the new major version.
- All existing CLI options (covering number, string, boolean, and array types) should continue to be properly registered and recognized by the argument parser when using the upgraded version.
- The fresh-instance initialization pattern used in tests should work using the library's standard factory call rather than the old subpath import and reset-state API.

## Why This Matters

Users running mocha with any of its command-line flags depend on those options being correctly parsed. If the underlying argument parsing library is out of date or incompatible, the CLI can break entirely or silently misbehave. Keeping the dependency up to date also ensures continued support and security fixes from the upstream library.
