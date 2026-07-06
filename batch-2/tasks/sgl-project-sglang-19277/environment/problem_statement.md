## Description

When debugging distributed model inference, engineers often need to inspect the full tensor that is sharded across multiple parallel workers. Currently, there is no tooling to reassemble those shards back into a single tensor for comparison or analysis.

This issue requests a new set of utilities for unsharding distributed tensors from multi-rank tensor dumps. The core capability needed is:

1. **Plan computation**: Given a description of which tensor dimensions are sharded across which parallelism axes, and per-rank parallelism metadata, compute a structured reassembly plan that identifies which rank holds which slice and in what order slices should be concatenated.

2. **Plan execution**: Given the reassembly plan and a mapping from world ranks to their tensor shards, concatenate the shards in the correct axis-rank order to reconstruct the full tensor.

3. **Metadata normalization**: Accept parallelism rank/size metadata from multiple distributed training frameworks into a common internal format. Axes with size 1 (non-parallelized) should be ignored.

## Expected Behavior

- When world ranks arrive in a different order than their logical axis positions ("scrambled"), the reconstructed tensor must still be numerically identical to the original.
- If parallelism metadata from more than one framework is present simultaneously, an error should be raised.
- Appropriate errors must be raised for invalid inputs such as empty rank lists, inconsistent axis sizes across ranks, or missing metadata for a sharded axis.
- Attempting to unshard a tensor sharded across multiple independent parallelism axes simultaneously should raise an unsupported-operation error.

## Why This Matters

Developers comparing tensor activations across different distributed configurations (e.g., different tensor-parallelism degrees) cannot do so without first reassembling the per-rank shards into a full tensor. This tooling enables that workflow automatically.
