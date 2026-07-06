## Description

When using the ORM's lazy database connection initialization, starting many concurrent transactions immediately after configuration fails with errors. The ORM defers establishing the physical database connection until the first query is executed, which is the documented behavior. However, when multiple transactions are started concurrently before any connection has been established, they can interfere with each other during the initialization phase, causing failures.

Simple (non-transactional) concurrent queries already handle this case safely — they serialize the connection setup and then proceed independently. But concurrent transactions have a race condition where multiple tasks can each attempt connection initialization simultaneously, leading to errors.

## Expected Behavior

- Running many concurrent simple queries on a freshly initialized (but not yet physically connected) database must succeed without errors.
- Running many concurrent transactions on a freshly initialized (but not yet physically connected) database must also succeed without errors.
- Only one physical connection initialization should occur even when many transactions start at the same time.

## Why This Matters

Applications that initialize the ORM and then immediately dispatch parallel background transactions (e.g., at startup or in test environments) should work reliably. Without this fix, such applications experience intermittent failures depending on concurrency timing.
