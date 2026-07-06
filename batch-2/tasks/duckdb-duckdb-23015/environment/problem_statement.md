## Description

The CI test runner currently produces failure output that is hard to read and act on. When a test batch fails, the output shows generic batch identifiers (e.g., "failed test batch 0") instead of naming the specific failing test, dumps full raw stdout and stderr blocks, and can repeat the same error details multiple times across retry attempts. When tests time out or crash with a fatal signal, there is no clean indication of which specific test is the likely culprit.

## Expected Behavior

- Failure messages should name the specific failing test file, not a generic batch identifier
- Wrong-result failures should be summarized as a concise one-line mismatch: "expected X, got Y; \<mismatch context\>"
- A relevant code snippet from the test file should be shown alongside wrong-result failures
- When a test times out and there is progress information available, the first unfinished test in the batch should be identified as the likely culprit
- When a test times out and no progress information is available, the last test in the batch should be named
- Fatal signal failures (e.g., segmentation faults) in stdout should name the last test that was being run
- When a test passes on a retry after previously failing, the output should clearly indicate recovery and show the failure details only once
- The final failure summary after exhausting all retries should be compact, without verbose batch/attempts headers

## Why This Matters

Developers debugging CI failures currently have to sift through large blocks of raw output to understand what went wrong and which test to reproduce. Clean, specific failure messages make it much faster to identify the failing test, understand the nature of the failure, and reproduce it locally.
