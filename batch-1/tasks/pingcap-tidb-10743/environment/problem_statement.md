## GC Delete Range Should Be Resilient to Individual Store Failures

Currently, when the GC worker processes its queue of delete range tasks, a failure on any single storage node causes the entire batch to abort immediately. This is far too aggressive: a transient error on one node stops all remaining ranges from being cleaned up in that GC cycle, even ranges that have nothing to do with the failing node.

The problem is made worse by the fact that the system does not detect all failure modes. If a storage node returns a malformed or error-carrying response — rather than a straightforward communication failure — the issue goes unnoticed and the range may be incorrectly marked as completed.

## Expected Behavior

- When processing a delete range task fails for one or more storage nodes, the GC worker should log the error and move on to the next task, rather than stopping the entire batch.
- A range that could not be successfully deleted on all nodes must remain in the pending queue so it is retried in a subsequent GC cycle.
- The same resilient behavior should apply to the "redo" delete ranges process.
- All error conditions from a storage node should be detected: outright communication failures, absent or empty responses, and responses that contain an embedded error message.

## Why This Matters

Transient storage failures should not block garbage collection of unrelated data. The current all-or-nothing behavior means a brief blip on one store can leave stale data from many unrelated jobs sitting around far longer than necessary.
