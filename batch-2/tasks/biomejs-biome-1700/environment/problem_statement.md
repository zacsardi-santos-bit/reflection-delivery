## Description

When using path-based configuration overrides to specify different global variables for different directories, the linter does not correctly apply the globals defined in the override section. Instead of recognizing variables listed in an override's globals for files that match the override's path pattern, the linter appears to ignore the override globals entirely, causing those variables to be incorrectly flagged as undeclared.

## Expected Behavior

- When a file matches a path-based override, the global variables listed in that override should be recognized as declared for that file.
- Variables that are listed as globals specifically within the override block should not trigger undeclared variable warnings for files in the matching path.
- The override's globals should take effect correctly so that, for example, test-specific globals configured for a test directory are not incorrectly flagged as undeclared in test files.

## Current Behavior

Globals defined inside override sections are not properly applied to matched files. Files that fall under an override's include path still get undeclared variable errors for variables that the override is supposed to declare as globals.

## Why This Matters

This makes it impossible to use path-based overrides to configure environment-specific globals (e.g., test framework helpers for test directories), which is a common use case for projects that have different global environments in different parts of the codebase.
