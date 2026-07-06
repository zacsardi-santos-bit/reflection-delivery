## Description

The Cairo compiler's optimization pipeline supports a configurable list of functions that can be freely moved (reordered) during compilation. When setting up end-to-end test databases, the current default optimization settings allow too many functions to be moved, which causes the generated code output to differ from what the test snapshots expect. This makes a large number of end-to-end tests fail.

There is currently no convenient way for test infrastructure to opt into a smaller, predictable set of moveable functions. A focused builder method on the optimization configuration type is needed so that tests can easily configure a minimal, stable set of moveable functions.

## Expected Behavior

- The optimization configuration type should offer a builder method that restricts moveable functions to a minimal set suitable for testing.
- This method should follow the same builder pattern as other configuration methods (accepting and returning the configuration by value so it can be chained with other builder calls).
- When test databases are initialized with this minimal configuration, all end-to-end tests should produce consistent, deterministic output that matches their snapshots and pass.

## Why This Matters

Without a minimal moveable-functions configuration, end-to-end tests produce non-deterministic or snapshot-mismatched output because the optimizer moves too many functions. Having a dedicated method makes it easy for tests to opt into a controlled optimization environment, ensuring stable and reproducible test results across the entire test suite.
