## Description

When using gradient checkpointing (recompute) together with pipeline parallelism, training crashes during the backward pass if the recomputed function captures tensors from its surrounding scope (closures). Pipeline-parallel training routinely frees the memory of intermediate tensors after the forward pass as a memory optimization. When backward tries to re-run the forward, these closure-captured tensors are no longer valid, causing the recomputed forward to fail.

## Expected Behavior

- The gradient checkpointing utility should detect tensors captured in a function's closure at forward time and save protected backup copies.
- When the backward pass is about to re-execute the forward function, any closure tensors that were freed by the pipeline should be transparently restored from their backups.
- This protection should work for plain functions, for layer objects whose forward method captures tensors in its closure, and for all supported options (such as RNG-state preservation disabled, non-reentrant mode).
- Gradients produced with this protection must be numerically identical to those produced without gradient checkpointing.
- Edge cases must be handled gracefully: functions with no closure, closures holding non-tensor values, and closures with empty (already-released) cells should all complete without error.

## Why This Matters

In large-scale pipeline-parallel training, it is common and necessary to release intermediate tensor memory between stages. Without closure protection in the gradient checkpointing path, any model that defines its forward logic using closures over external tensors will silently fail or crash during backward, making it impossible to combine these two memory-saving techniques.
