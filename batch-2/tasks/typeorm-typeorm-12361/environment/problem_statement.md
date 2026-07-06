## Description

TypeORM v1 changed how the file-based query logger resolves log file paths: it now uses the current working directory instead of the application root. This is a breaking change for any project that is not started from its root folder — relative log paths that worked correctly before will now silently write log files to an unexpected location.

Developers migrating to TypeORM v1 may not realize their log paths are affected unless they inspect every place in their codebase where the file logger is instantiated. This is error-prone to do manually, especially in large projects.

## Expected Behavior

A new automated migration transform should be added to the codemod package that:

- Scans source files and finds every instantiation of the file logger imported from TypeORM, regardless of how it was imported (named import, aliased import, namespace import, CommonJS require, etc.)
- Inserts a warning comment immediately before any instantiation where the log path could be affected — specifically, when no log path is provided, or when a relative log path is used
- Skips instantiations that already use an absolute log path (since those are unaffected by the change)
- Skips cases where the options are dynamic or spread-only (since the path cannot be statically determined)
- Does not modify files where the logger is not imported from TypeORM
- Inserts only one warning comment per statement, even if a statement contains multiple logger instantiations

## Why This Matters

Without this migration aid, developers upgrading to TypeORM v1 may end up with log files written to unexpected locations on disk after the upgrade. The automated transform helps them identify all at-risk usages quickly and decide whether to switch to absolute paths.
