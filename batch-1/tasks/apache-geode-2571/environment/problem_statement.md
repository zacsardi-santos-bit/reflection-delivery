## Description

The distributed transaction commit handling code has grown into large, complex methods that are impossible to unit test in isolation. Two key behaviors in the transaction recovery flow — checking whether a transaction is already in progress, and sending a query to remote members when the originating node departs the cluster — are embedded deep inside monolithic methods with many side effects and external dependencies. This makes it impossible to verify their correctness without spinning up an actual distributed system.

## Expected Behavior

The transaction tracking and member-departure recovery logic should be refactored so that:

- The logic for determining whether a transaction is currently being processed can be checked independently, including for null inputs
- The logic for looking up a transaction's commit status (whether it is in progress, found in history, or neither) should be clearly separated and verifiable in isolation
- When a cluster member departs and the transaction has not yet been acknowledged, the system should create and send a commit-process query to the remaining members, then wait for replies — and this behavior should be independently testable without a live cluster

## Why This Matters

Without unit tests for these behaviors, regressions in the transaction recovery path could go undetected until they appear in production under failure scenarios (e.g., a node crashing mid-transaction). Extracting these behaviors into individually testable units gives developers confidence that transaction recovery works correctly under member-departure conditions.
