## Description

When using windowed expressions that partition data into groups, it is currently possible to accidentally specify the same column name more than once as a partition key without getting any error. This silent acceptance of duplicate partition keys is confusing — grouping by the same column twice is redundant and likely indicates a bug or typo in user code, yet the library silently proceeds.

## Expected Behavior

- If a user specifies duplicate column names as partition keys in a windowed expression, the library should detect this and raise a clear duplicate-key error immediately.
- The error should be raised at evaluation time so users get immediate, actionable feedback about the mistake.
- Windowed expressions with non-duplicate partition keys should continue to work as before.

## Why This Matters

Silent acceptance of duplicate partition keys can mask bugs and make it hard to trust windowed expression results. By raising an explicit error, users get clear feedback about typos and accidental repetitions in their partition specifications, making it much easier to write correct queries.
