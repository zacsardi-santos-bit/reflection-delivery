# RocksDB State Stores Should Track Lifecycle Status and Persist Committed Offsets

## Description

Currently, Kafka Streams' RocksDB-backed state stores do not persistently record whether a store was cleanly closed or crashed while open. When a store is reopened after an unclean shutdown, there is no way to detect that it may contain uncommitted data. This is particularly risky when running with exactly-once delivery guarantees, where reading uncommitted data could lead to incorrect results or data corruption.

Additionally, the committed changelog offsets are not stored inside RocksDB, making it impossible to recover the last known consistent offset purely from the store itself.

## Expected Behavior

- Each state store should maintain a dedicated internal column family to track its open/closed lifecycle status and the committed offsets for each changelog partition.
- When a store is cleanly closed, the status should be persisted as "closed."
- When a store is reopened:
  - If the persisted status indicates a clean close (or the store is fresh), the store should open normally.
  - If the persisted status indicates the store was left open (indicating a potential crash), deployments with exactly-once semantics must refuse to open the store and raise an error indicating invalid state. Deployments without exactly-once guarantees should be allowed to proceed.
- Committed offsets written to the store should be retrievable after reopening, even if the previous shutdown was unclean.
- When a user attempts to disable the atomic flush setting on the RocksDB adapter, the request should be ignored and a warning should be logged, since atomic flush is required for correct operation.

## Why This Matters

Without this tracking, Kafka Streams has no reliable mechanism to detect corrupted or uncommitted state after a crash. Adding persistent lifecycle status and offset tracking enables safer recovery behavior — particularly for exactly-once workloads — and allows the system to recover the last committed state reliably after both clean and unclean restarts.
