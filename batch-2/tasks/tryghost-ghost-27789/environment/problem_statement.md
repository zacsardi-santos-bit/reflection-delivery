# Bug: Saving a member crashes when a label is missing its name

## Description

When saving a member through the API and one of the labels in the payload is missing its name field (for example, an object that only contains an id), the save operation throws an uncaught error instead of handling the invalid label gracefully.

This happens because the code that normalizes labels before saving assumes every label object has a name, and immediately attempts a string operation on it. If the name is absent, that operation fails at runtime.

## Expected Behavior

- Labels with no name should be silently skipped and not included in the saved set.
- Labels whose name consists entirely of whitespace should also be discarded.
- Labels that are case-insensitive duplicates of an already-accepted label should be deduplicated — only the first occurrence is kept.
- A member save with a mix of valid, nameless, whitespace-only, and case-duplicate labels should succeed and result in only the valid, unique labels being stored.

## Why This Matters

The API does not always guarantee that every label object in an incoming request includes a name. When this happens today, the entire member save silently fails with a crash. This is a data-integrity and reliability issue — a single malformed label in an otherwise valid request should not bring down the whole operation. The system should be resilient to these edge cases and simply ignore the bad label entries.
