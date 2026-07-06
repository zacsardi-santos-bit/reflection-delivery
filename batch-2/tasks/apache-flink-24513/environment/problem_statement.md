## Description

When file-merging checkpointing is enabled, operators with no actual user state are not properly tracked in the checkpoint state lifecycle. These "empty" operators still have managed checkpoint directories created for them, but since they do not produce a real state handle, those directories are invisible to the shared state registry — meaning they could be deleted prematurely before the checkpoint is actually retired.

Additionally, state handle types that are part of the file-merging checkpoint system are currently placed in a checkpoint-internal package, even though they represent general state management concepts that belong with other state handle types under the broader state management hierarchy.

## Expected Behavior

- State handle classes used by the file-merging checkpoint system should be located in the state management package (not the checkpoint-internal package), and all existing references should be updated accordingly.
- Even when an operator has no state, a file-merging-aware state handle should be returned by the snapshot, acting as a placeholder that registers the managed directories with the shared state registry.
- Registered managed directories (both the task-owned exclusive directory and the per-subtask shared directory) should remain on the filesystem for as long as the corresponding checkpoint is active, and should be deleted from the filesystem only after they are no longer referenced by any retained checkpoint.

## Why This Matters

Without this fix, stateless operators using file-merging checkpointing do not participate in the shared state registry lifecycle, which may cause their managed directories to be deleted too early or leave directory management in an inconsistent state. Proper registration ensures directories are cleaned up correctly when the checkpoint they belong to is discarded.
