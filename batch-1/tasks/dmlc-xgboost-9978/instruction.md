Implement fixes and enhancements to the multi-target tree building functionality in XGBoost to address issues with learning rate scaling, split condition initialization, and unsupported distributed training. Update the gradient generation helper to support multi-target scenarios.

*   Ensure that changing the learning rate when building a multi-target tree using the quantile histogram method only scales leaf output values proportionally:
    *   Tree structures (split conditions and thresholds) must remain identical for trees built with learning rates in a fixed ratio.
    *   Implement this in the multi-target histogram evaluator by ensuring leaf weight recalculation is independent of the learning rate.

*   Initialize split conditions for leaf nodes in multi-target trees to NaN:
    *   Update `MultiTargetTree::Expand` in `src/tree/multi_target_tree_model.cc` to resize the `split_conds_` vector using `std::numeric_limits<float>::quiet_NaN()` as the fill value.
    *   Ensure `RegTree::SplitCond(nidx)` returns NaN for leaf nodes.

*   Maintain consistent split conditions for internal nodes across different learning rates:
    *   Ensure `RegTree::SplitCond(nidx)` returns a finite value for internal nodes, consistent across trees built from the same data.

*   Update the gradient generation helper to support multi-target inputs:
    *   Add a new overload of `GenerateRandomGradients` in `tests/cpp/helpers.h` with the signature `GenerateRandomGradients(ctx: Context const*, n_rows: bst_row_t, n_targets: bst_target_t) -> linalg::Matrix<GradientPair>`.
    *   Ensure the returned matrix has shape [n_rows, n_targets] and that Shape(1) equals `n_targets`.
    *   Retain the existing single-argument overload for single-target use.

*   Implement a guard against unsupported distributed training with multi-target trees:
    *   In `MultiTargetHistBuilder::InitData` within `src/tree/updater_quantile_hist.cc`, raise a fatal error if `collective::IsDistributed()` is true.
    *   Disable the corresponding column-split multi-target test using the `DISABLED_` prefix.

*   Ensure multi-target trees report correct metadata:
    *   Trees built with `n_targets > 1` must report `IsMultiTarget()` as true and `NumTargets()` equal to `n_targets`.
    *   Leaf values must be accessible via `GetMultiTargetTree()->LeafValue(nidx)` as a span of size `n_targets`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.