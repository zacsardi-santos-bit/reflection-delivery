## Description

The CDC system conflates two distinct DDL filtering concerns into a single operation, causing DDL events for tracked tables to be incorrectly dropped too early in the pipeline.

Currently, the logic that determines "should this DDL be completely ignored (out of scope)" and "should this DDL be excluded from downstream delivery (in scope but user-configured to skip)" is merged into one check. As a result, the DDL puller discards events — such as column additions — based on transaction start timestamp values and other user-configured exclusion rules, even when those tables are within the replication scope. These events never reach the manager level where the full context needed to evaluate finer-grained rules is available.

## Expected Behavior

Two separate filtering concepts should be implemented:

- **Discard**: A DDL is completely out of scope — wrong DDL type (not in the allowed list) or the table/schema doesn't match the configured replication rules. A discarded DDL is not tracked in internal schema storage and is not sent downstream.
- **Ignore**: A DDL is in scope, but the user has configured it to be excluded from downstream delivery based on transaction timestamps, SQL query patterns, or event type filters. An ignored DDL is tracked in schema storage but not sent to downstream.

The coarse "discard" check (which requires only the DDL type and table/schema name) should be performed at the puller level. The finer "ignore" check (which requires the full DDL event including timestamp, query text, and event type) should be applied at the manager level.

The DDL puller should stop using the job's start timestamp as a criterion for skipping events — that responsibility belongs at a higher level with access to the complete event context.

## Why This Matters

Without this separation, tracked tables can have their DDL events silently dropped before they reach the stage where event filters are properly evaluated. This leads to incorrect replication behavior: DDL events that should be tracked in schema storage (to keep the schema in sync) but not forwarded downstream are instead being completely discarded, potentially breaking table replication.

Integration test coverage should also be expanded to verify that DDL events filtered by event type (such as table truncation or schema alteration) are handled correctly end-to-end.
