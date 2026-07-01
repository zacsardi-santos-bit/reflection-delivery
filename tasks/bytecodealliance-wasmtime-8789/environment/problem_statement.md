## Description

The WebAssembly runtime test suite has a duplication problem: many tests for function-calling behaviors only run against the default compiler backend, while a separate file maintains a handful of hand-written duplicates for the alternative backend. This creates maintenance overhead and leaves coverage gaps — bugs in the alternative backend go undetected for any scenario not explicitly duplicated in that separate file.

The solution is to extend the shared test annotation so tests automatically run against all supported compiler backends, and to remove the now-redundant backend-specific duplicates.

## Expected Behavior

- Tests annotated with the shared testing macro should generate and run separate variants for each supported compilation strategy (e.g., one variant per backend).
- The macro should accept a declarative attribute for enabling specific WebAssembly features, so tests no longer need to set up feature flags inline in their body.
- When specific WebAssembly features are declared, the macro should only run variants for backends that support those features.
- The standalone file of alternative-backend-specific tests should be stripped of the tests that are now covered by the shared suite.

## Why This Matters

Currently, a test that exists only in the shared suite is effectively only verified against one backend. Any regression in the alternative backend is invisible unless someone manually adds it to the separate file. By having the shared macro generate per-backend variants automatically, every new test immediately gets cross-backend coverage with no extra effort.
