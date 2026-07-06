## Description

When using the thread-safe variants of the map and set containers, calling a remove operation from within an iteration callback causes the program to deadlock. This is a common and practical use case — for example, filtering out elements from a collection while iterating over it — and it should work without issue.

## Expected Behavior

- Iterating over a thread-safe map and removing entries from the same map within the callback should complete successfully without deadlocking.
- Iterating over a thread-safe set and removing entries from the same set within the callback should complete successfully without deadlocking.
- After the iteration completes, the collection should correctly reflect all removals made during the callback: removed elements should be gone, and untouched elements should remain.

This behavior should work for all thread-safe map types (any-to-any, int-to-any, int-to-int, int-to-string, string-to-any, string-to-int, string-to-string) and all thread-safe set types (any, int, string).

## Why This Matters

A typical use case is selectively deleting entries from a concurrent map or set in a single pass — without first collecting keys into a separate slice and then removing them in a second pass. The current behavior makes this simple pattern impossible and forces workarounds that add complexity. Fixing the deadlock enables a natural and efficient coding style for concurrent container modifications.
