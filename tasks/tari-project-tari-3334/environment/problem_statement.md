## Description

Writing blockchain storage tests currently requires a lot of repetitive boilerplate: each test that needs a real database backend must manually create a temporary directory, pass that path around to database constructors, and then remember to delete the directory when the test finishes. This makes tests verbose, hard to read, and fragile — cleanup failures are silently swallowed, meaning leftover test data can accumulate without any warning.

## Expected Behavior

- A self-contained temporary database type should be available in the shared test helpers, constructable without any path or configuration arguments
- This type should be usable anywhere a real database backend is currently used in tests (i.e., interchangeable with the existing on-disk database type in test contexts)
- Helper functions for building test blockchains should accept this new type directly, removing any requirement for callers to manage filesystem paths
- Where tests still explicitly clean up temporary directories, failures during cleanup should be reported as test failures rather than silently ignored

## Why This Matters

Eliminating manual path management reduces the amount of boilerplate in each test, makes individual tests easier to understand, and makes cleanup failures visible. Tests become simpler, more self-contained, and less likely to leave stale data behind.
