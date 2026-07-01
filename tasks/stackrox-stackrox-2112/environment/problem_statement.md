## Description

Several storage and search sub-packages for image vulnerability data and image data are currently placed under restricted directories that limit their visibility in Go's package system. This restriction prevents code outside the immediate parent tree from importing these packages, creating a hard boundary that blocks integration across components.

In practice, a background process that automatically lifts vulnerability suppression after the suppression expiry period cannot be fully tested because the storage packages it depends on are unreachable from the test package. The suppression unsuppression flow — which should query for vulnerabilities whose suppression has expired and then clear their suppressed status — cannot be wired together in an integration test.

## Expected Behavior

- The storage and search sub-packages for image CVE data and image data should be importable from any package in the codebase, not just their immediate parent tree.
- After moving these packages to accessible locations, an integration test should be able to create a complete pipeline (storage + indexer + searcher + reprocessor loop) and verify that suppressed vulnerabilities with expired suppression timestamps are correctly unsuppressed when the reprocessor runs.
- All existing tests that previously imported from the restricted paths should continue to compile and pass after the paths are updated.

## Why This Matters

Making these packages accessible enables proper integration testing of the vulnerability unsuppression feature against a real database backend. It also allows other parts of the system to reuse these building blocks directly, reducing duplication and making the architecture more composable.
