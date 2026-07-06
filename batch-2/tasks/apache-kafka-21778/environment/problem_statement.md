## Simplify State Manager API: Merge Separate Persistence Operations into a Single Unified Operation

### Description

The current state management API for Kafka Streams tasks exposes two separate operations for persisting state: one for flushing store data and another for writing the checkpoint file with current offsets. Any code that needs to safely persist task state must call both operations in the correct order. This two-step model is unnecessarily complex and error-prone — it's easy to flush without checkpointing or to checkpoint stale data.

Additionally, the decision of whether to actually write a checkpoint is gated by a threshold: checkpointing only happens if the changelog offsets have advanced by a significant amount since the last snapshot. While this was intended as an optimization, it adds complexity and can lead to situations where a checkpoint is expected but silently skipped.

### Expected Behavior

- The state manager interface should expose a single unified persistence operation that both persists registered state store data and writes the checkpoint file.
- The previous two separate operations should be removed from the interface.
- Tasks' periodic state persistence method should require no arguments — callers should not need to specify whether to "force" the operation, since the unified operation is always meaningful to run.
- When the unified operation encounters an error from a state store, it should raise the appropriate exception type consistently.

### Why This Matters

Simplifying to a single unified persistence operation makes the API easier to understand and harder to misuse. It removes the need for callers to coordinate two separate calls and eliminates the subtle threshold-based skip logic that could cause checkpoints to be silently dropped. This also makes the lifecycle of task state persistence more predictable and easier to reason about when tasks are suspended, closed, or restored.
