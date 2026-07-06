## Description

The CI test runner needs two enhancements to improve test stability detection and output clarity.

First, when a test exceeds its expected runtime, the current output blends in with normal log lines, making it easy to miss. These messages should be prefixed with a clear warning label so CI operators can quickly spot performance regressions in the log stream.

Second, the runner has no mechanism to verify that newly added or selected tests are actually stable — a test might pass once during CI but intermittently fail on reruns. We need a mode that reruns individual tests multiple times to confirm stability before accepting them. Fast tests should be rerun more aggressively than slow tests, since the cost of re-running a fast test is low.

## Expected Behavior

- When a test's runtime exceeds the threshold, the log line is clearly prefixed to indicate a warning condition.
- A new flag allows the runner to enter "stabilization" mode, where each test in the list is individually rerun a fixed number of times according to whether it is classified as fast or slow.
- When running with a changed test list, newly added tests (those not present in the base list) are automatically stabilized using the same per-test rerun policy.
- When the number of newly added tests is very large (more than 500), the entire batch is rerun a small fixed number of times rather than repeating each test individually, to keep total run time manageable.
- If any stabilization rerun fails, the pipeline immediately reports an overall failure with a clear error message.

## Why This Matters

Without these changes, flaky or slow tests can silently pass during a single CI run and only surface later. The stabilization mode provides an automated safety net, and the clearer warning output makes performance regressions visible without manual log inspection.
