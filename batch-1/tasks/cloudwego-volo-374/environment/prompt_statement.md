I recently upgraded the CI pipeline for this project to run strict linting — treating all warnings as hard errors — in addition to the usual type checking. The problem is that the build now fails on the very first step and never gets to run any tests.

The failure happens when linting the HTTP library with no optional features enabled: there's a macro in the HTTP context module that is only used when certain features (client or server) are active. When neither is enabled — which is the case in the no-features lint check — the macro exists but is never referenced, so the linter flags it as an "unused macro" and treats it as an error.

Because that step fails, the subsequent test runner step never executes at all, so all 15 tests in the core networking crate show as failures. The tests themselves are fine — they're testing pre-existing functionality that works correctly. The tests cover things like a smart pointer utility type (verifying it properly implements clone, deref, borrow, and display traits), a buffered reader, static service discovery, and various load balancing strategies (consistent hash and weighted random). They just can't run because the build pipeline is broken at an earlier step.

I need to fix this so the linting step passes cleanly and the tests can actually run.
