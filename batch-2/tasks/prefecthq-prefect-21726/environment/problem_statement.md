## Description

The bulk task run event recorder fails with a database integrity error when it encounters conflicting events — either two events that describe the same logical task (same flow run, task key, and dynamic key) but carry different internal identifiers, or events sharing the same identifier that arrive multiple times with updated metadata. This causes the entire batch to fail and forces the consumer to implement costly retry logic that doesn't actually resolve the conflict.

## Expected Behavior

- When conflicting events arrive (whether in the same batch or across separate batches), the recording system should resolve the conflict gracefully by updating the existing task run with the latest state and metadata rather than raising an error.
- When two events describe the same logical task but carry different identifiers, the system should consolidate them onto a single canonical record. The duplicate identifier should not persist in the database.
- All state records from conflicting events should be attached to the canonical task run, preserving the complete chronological state history. Both the state's task run reference and the state's internal detail reference must point to the canonical record.
- Chains of conflicts (where one event's identifier overlaps with an existing record and another event's natural key overlaps with the first) should be fully coalesced onto a single canonical record.
- Bulk insert operations should order rows by the natural conflict key to guarantee consistent lock acquisition across concurrent recorders, preventing deadlocks.

## Why This Matters

In distributed orchestration environments, duplicate or out-of-order task run events naturally occur — tasks can be retried, events can be replayed, and multiple workers may emit events for the same logical task simultaneously. The current behavior turns these normal operational conditions into pipeline failures. Graceful conflict resolution keeps the system running and produces accurate, complete state history on the surviving canonical record.
