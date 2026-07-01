## Description

When training a gradient boosted tree model with multiple output targets using the histogram-based tree builder, there is a bug in how the learning rate interacts with the leaf weight calculation. Specifically, changing the learning rate should only scale the predicted leaf values proportionally — it should not affect which features are selected for splits, or at what thresholds splits are made. This invariant holds correctly for single-target trees, but is broken for multi-target trees.

Additionally, the split condition stored at leaf nodes in multi-target trees is not initialized to a well-defined sentinel value. The expected convention is that leaf nodes in multi-target trees carry a not-a-number (NaN) split condition (since there is no split at a leaf), but currently these values are left at whatever default was in memory.

Finally, there is no guard against attempting distributed (column-split) training with the multi-target histogram builder, which is not yet implemented. This should fail with a clear error rather than producing incorrect results silently.

## Expected Behavior

- Two multi-target trees built from the same data with learning rates in a fixed ratio must have identical tree structures (same splits at same thresholds). Only the leaf output values should differ, scaled proportionally to the learning rate ratio.
- Leaf nodes in multi-target trees must report a NaN split condition.
- Attempting distributed multi-target training with the histogram builder must immediately raise a fatal error.
- A helper for generating random gradient data must support producing multi-target gradient matrices (one gradient pair per target per sample).

## Why This Matters

This bug means that multi-target histogram tree models do not respect the learning rate parameter in the expected way, which undermines model correctness and reproducibility. Any code that relies on the learning rate scaling leaf values consistently will produce wrong results for multi-target regression.
