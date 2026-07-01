Suppress the unused macro lint error in the HTTP context module to ensure the strict linting build step succeeds when building the HTTP library without optional features. This will allow the test suite to execute.

*   Suppress the unused macro lint error:
    *   Identify the macro in the HTTP context module that triggers the unused macro warning when neither the client nor server feature is enabled.
    *   Apply an appropriate attribute or directive to the macro to suppress the unused macro lint warning.
*   Ensure the HTTP library compiles cleanly under strict linting without optional features enabled.
*   Verify that all 15 existing tests in the core networking crate compile and run successfully once the linting step passes:
    *   Smart pointer utility type tests: Verify clone, deref, borrow, and display trait implementations, and construction from both reference-counted and borrowed forms.
    *   Buffered reader tests: Verify compaction behavior and Unpin trait compliance.
    *   Static service discovery test.
    *   Load balance service layer test.
    *   Consistent hash load balancer tests: Verify basic node selection, key consistency, behavior when nodes change, and statistical distribution.
    *   Weighted random load balancer test.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.