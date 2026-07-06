## Description

The ColossalAI inference system needs a structured batch management layer to efficiently group and process sequences together during language model inference. Currently, sequences manage their own memory state individually and the inference pipeline lacks clean abstractions for transitioning sequences through the prefill and decode stages.

## Expected Behavior

- A new batch container class should allow grouping multiple sequences together, allocating KV cache memory for them collectively, and managing their lifecycle as a unit
- When sequences are added to a batch, the batch should track their lengths and memory block assignments, starting with unallocated (negative) block indices
- The batch should support appending new decode tokens to all active sequences at once
- It should be possible to remove individual sequences from the batch and merge two batches together
- Clearing a batch should release all associated memory and reset all tracking state
- The KV cache manager should expose distinct operations for allocating memory during the initial prefill phase versus incremental decode steps
- The KV cache manager should support freeing memory for a single sequence (one-dimensional) or for multiple sequences (two-dimensional) in a single call
- Each internal cache block should track its own available space, and that space should be fully restored after memory is freed
- The sequence lifecycle tracking in the running list should be extended so that sequences can be explicitly marked as actively running and then transitioned from the prefill phase to the decode phase as a batch operation
- The sequence data structure should no longer accept a separate block table at construction time, since block tables are now managed externally by the batch container

## Why This Matters

These improvements make it possible to build a more efficient and correct batched inference serving loop where prefill and decode operations are clearly separated, memory allocation is transparent and reversible, and sequence lifecycle transitions are explicit and verifiable.
