## Support snapshot testing with Web Test Runner

### Description

The snapshot comparison feature in the DOM testing library currently only works when using Karma as the test runner. All snapshot tests in the web test runner test suite are explicitly skipped with a comment: "TODO: skipped until web test runner supports snapshots."

The Web Test Runner now has snapshot support via an asynchronous API. The library should be updated to detect the test runner environment and use the appropriate snapshot mechanism. Since the Web Test Runner reads and writes snapshot files asynchronously, the snapshot assertion needs to return an awaitable result so test authors can await it.

### Expected Behavior

- Snapshot assertions should work in the Web Test Runner environment, not just Karma
- The assertion should return an awaitable result so test functions can await it
- When a snapshot doesn't match, the assertion should throw an error with both the actual and expected HTML values so developers can see a diff
- Negation (asserting that content does NOT match a stored snapshot) should also work
- Options for ignoring certain attributes or tags during comparison should continue to be supported
- Both assertion styles supported by the library should work, including chained DOM accessors for light DOM and shadow DOM

### Why This Matters

Developers who have migrated to the Web Test Runner cannot use snapshot testing for their web components at all — the tests are silently skipped. Fixing this allows snapshot-based regression testing to work in both Karma and Web Test Runner environments, giving developers confidence that their component rendering hasn't changed unexpectedly.
