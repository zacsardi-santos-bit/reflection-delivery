# Integration Tests Use Unreliable Fixed Sleeps for Replication Sync Checks

## Description

The integration tests currently rely on fixed time delays (e.g., sleeping for 5 seconds) before checking whether replica instances have caught up with the primary. This approach is fundamentally unreliable: the sleep may be too short on slow machines or under load, causing flaky test failures, or unnecessarily long, slowing down the test suite.

Additionally, there is no way for the tests to directly verify the primary's current write-ahead log position before waiting for replicas to catch up. The synchronization check only compares whether all keepers report the same position — it does not verify that position meets a known minimum from the primary.

## Expected Behavior

- The test infrastructure should be able to open a dedicated replication protocol connection to any keeper or proxy, in addition to its normal SQL connection.
- A helper function should be available to query the primary's current write-ahead log position directly via the replication protocol.
- A helper function should then use that position as a lower bound when waiting for all specified keepers to report that they have reached or exceeded that position.
- The synchronization wait function should accept the actual keeper objects (not just their string identifiers) and the known minimum log position, and should return an error if synchronization is not achieved within the timeout.
- The fixed sleep-then-check pattern should be replaced with: first read the primary's current log position, then wait until all relevant keepers reach or exceed it.

## Why This Matters

Replacing arbitrary sleeps with evidence-based polling makes the integration tests deterministic and robust. Tests will pass as soon as synchronization is actually confirmed, rather than after an arbitrary delay, and will fail immediately with a clear error if synchronization does not happen within a reasonable timeout.
