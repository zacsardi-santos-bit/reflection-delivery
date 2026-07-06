## Description

After upgrading the CI pipeline to run strict linting (treating all warnings as hard errors), the build now fails before any tests can execute. The failure happens when building the HTTP library package without optional features enabled: a macro defined in the HTTP context module is flagged as "unused" under strict linting because the macro is only referenced from code that is conditionally compiled under specific features. When neither the client nor server feature is enabled (the base case), the macro exists but is never invoked, causing the lint to trigger as a hard error.

Because the strict linting step is a prerequisite to running the test suite, ALL tests in the core networking crate fail — not due to logic errors, but simply because the build pipeline never reaches the test execution phase.

## Expected Behavior

- The HTTP library should compile cleanly under strict linting when built without any optional features. The unused macro warning for a macro that is only active under certain feature combinations should be suppressed (since it is a known and intentional pattern).
- Once the linting step succeeds, all 15 existing tests in the core networking crate should execute and pass. These tests cover: a smart pointer utility type (clone, deref, borrow, and display trait implementations, construction from reference-counted and borrowed forms), a buffered reader (compaction and Unpin behavior), static service discovery, consistent hash load balancing (correctness, stability, change handling, and distribution), weighted random load balancing, and the load balance service layer.

## Why This Matters

Upgrading from basic type-checking to enforced lint compliance is a valuable quality improvement, but one pre-existing macro in the HTTP context module is blocking the entire build. Fixing this single issue would unblock the CI pipeline and allow the full existing test suite to run.
