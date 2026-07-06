## Description

In a multi-datacenter (active/standby) deployment, CHASM side-effect tasks that are generated on the active cluster get replicated to standby clusters. When a task has been pending on standby past a configured discard delay, the standby executor currently has no meaningful way to handle it — it simply drops the task. This means that if a failover occurs before the active cluster finishes processing the task, the work is silently lost.

## Expected Behavior

Task executors should be able to declare a custom "discard handler" — an optional action to take when a task reaches the standby discard deadline. When such a handler is registered, the standby task executor should invoke it rather than silently dropping the task.

For example, an activity dispatch task could spill itself into the standby cluster's task queue so that workers can pick it up once the standby cluster becomes active after a failover. Without a handler, the current behavior (task discarded with an error) should remain.

Specifically:

- There should be a way for a side-effect task executor to declare custom discard behavior by implementing an optional discard interface.
- The task registry should be able to report whether a given task type has a discard handler registered.
- The CHASM node tree and the higher-level tree interface both need a new operation to invoke the discard handler for a task.
- Standby task executors (outbound, transfer, and timer queues) must check for a discard handler before discarding: if one exists, call it; if not, return the standard discarded error.
- The existing side-effect task execution method should stop requiring the caller to pass in the registry explicitly — the registry should be obtained internally.

## Why This Matters

This change prevents silent work loss during failovers. By giving task executors the ability to pre-position work on the standby cluster before a failover happens, the system becomes resilient to situations where the active cluster did not process a task before losing leadership.
