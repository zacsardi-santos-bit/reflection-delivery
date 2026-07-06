## Description

Azkaban currently treats project names as case-sensitive, so "MyProject" and "MYPROJECT" are considered different projects. This is confusing and error-prone: a user can accidentally create multiple projects that differ only in letter casing, leading to ambiguity and potential conflicts. Project name uniqueness should be enforced in a case-insensitive manner.

## Expected Behavior

- When a user attempts to create a project whose name is the same as an existing active project's name (ignoring case), the system should reject the request with a clear error indicating a duplicate already exists.
- The in-memory project name cache should use case-insensitive lookups so that the in-memory check and the database check are consistent with each other.
- A general-purpose concurrent map data structure with case-insensitive string key support should be available for use across the codebase (not just for projects).

## Why This Matters

Without this fix, two projects named "myproject" and "MYPROJECT" can coexist in the system, causing user confusion about which project is which. Enforcing case-insensitive uniqueness at both the cache layer and the database layer ensures that the system behaves predictably regardless of how users capitalize project names.
