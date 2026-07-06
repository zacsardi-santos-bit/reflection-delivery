I'm working with the multi-target tree building functionality in XGBoost. There's a bug in the histogram-based tree builder for multi-target regression: when I change the learning rate, it should scale leaf output values proportionally but should not affect the tree structure — the same features should be chosen for splits at the same thresholds. This invariant works correctly for single-target trees, but is broken for multi-target trees. The weight calculation in the multi-target evaluator appears to be influenced by the learning rate when it shouldn't be.

There's also a related issue: leaf nodes in multi-target trees should report a NaN split condition (since leaves don't have splits), but they're currently not initialized to NaN. This causes incorrect behavior when comparing or reporting split conditions for leaf nodes.

Additionally, the multi-target histogram builder should explicitly reject distributed (column-split) training with a clear error message, since that combination is not yet supported. Right now there's no guard against this, which could lead to silent incorrect behavior.

Finally, the test helper that generates random gradient data needs to be extended to support multi-target scenarios, accepting a context and a target count alongside the row count so it can produce gradient matrices shaped for multiple targets.

Could you fix the weight recalculation bug in the multi-target histogram evaluator, initialize multi-target leaf split conditions to NaN, add the distributed training guard to the multi-target builder, and update the gradient generation helper to support multi-target inputs?
