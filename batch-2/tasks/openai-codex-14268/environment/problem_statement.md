## Description

The memory system has a function responsible for filtering and serializing conversation history items before they are used to generate memory summaries. Currently this function is private to its inner module, which makes it impossible to write focused unit tests that verify its behavior in isolation.

## Expected Behavior

- The filtering/serialization function should be accessible from the parent module's test code so it can be tested directly.
- When given an empty list of input items, the function should return a successful result containing a valid, parseable empty list — not an error.

## Why This Matters

Being able to test this function directly is important for verifying correctness of memory filtering logic as the system grows. Without access to the function in tests, regressions in edge cases (like handling empty input) cannot be caught early. Making the function accessible to tests enables developers to add targeted coverage for its behavior.
