## Description

Setting up a test for the Hummock storage layer currently requires a large amount of boilerplate in every single test function. Each test manually constructs an sstable store, event handler, version reader, meta client, and local storage — often 30–50 lines of setup code before a single line of test logic. This makes the test suite verbose, hard to maintain, and inconsistent in how different tests wire things together.

## Expected Behavior

- A shared test environment helper should be available that bundles all standard Hummock test setup into a single function call, returning a struct that holds the storage, manager, and meta client.
- The helper struct should expose convenient methods for common operations: registering a table by its numeric ID, registering a table with full catalog metadata, and committing an epoch (which internally handles sealing, syncing, committing to the meta service, and waiting for visibility).
- The storage object accessible via the test helper should also expose direct methods for sealing an epoch with a checkpoint flag, sealing and syncing an epoch in one step, retrieving the current version reader, and waiting for a given epoch to become visible.
- Several existing test utility functions should be renamed to follow a consistent naming convention that makes their role as test-only utilities immediately obvious from their name.

## Why This Matters

The boilerplate in each test is error-prone (it is easy to forget a step or wire components incorrectly) and obscures the intent of each test. By centralizing the setup into a reusable helper, new tests become easier to write, existing tests become easier to read, and the suite becomes more consistent overall. The renaming also improves discoverability and prevents accidental use of test-only utilities in production code paths.
