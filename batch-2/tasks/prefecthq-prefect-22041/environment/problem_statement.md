## Description

The Prefect server needs a reliable, idempotent message queue subsystem for tracking worker cleanup operations — tasks that must be performed after flow runs time out or are cancelled. Currently there is no dedicated mechanism to ensure cleanup work is delivered to workers reliably, retried on transient failures, or discarded after exhausting all retry attempts.

## Expected Behavior

- Cleanup tasks can be enqueued idempotently: sending the same cleanup request multiple times results in only one active task.
- Workers can claim a cleanup task via a lease-based reservation, ensuring no two workers process the same task simultaneously.
- Workers can acknowledge successful completion, release the task back for retry on failure, or renew their lease while work is in progress.
- When a lease expires without acknowledgment, the task is automatically made available again for redelivery.
- After a configurable number of failed delivery attempts, the task is moved to a dead-letter store rather than retried indefinitely.
- Workers can efficiently wait for new work to appear rather than polling, using a sequence-based wakeup notification mechanism.
- The queue is scoped by work pool, so operations on one pool cannot interfere with another.
- The system is pluggable: the backing store implementation is configurable, with an in-memory implementation provided by default.
- All relevant behavior (lease duration, maximum delivery attempts, idempotency retention) is controlled by server settings.

## Why This Matters

Without this subsystem, cleanup tasks for timed-out or cancelled flow runs have no guaranteed delivery path. Workers could miss cleanup work, retry it redundantly, or have no efficient way to wait for new cleanup tasks to arrive. This change provides the infrastructure needed to make worker-driven cleanup operations reliable and observable.
