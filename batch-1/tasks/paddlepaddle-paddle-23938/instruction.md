Implement helper functions and validation checks in the mixed-precision and distributed training components of the deep learning framework. Add graph-traversal utilities to the mixed-precision module and improve configuration validation in the collective distributed optimizer.

*   Implement `find_op_index` in `python/paddle/fluid/contrib/mixed_precision/fp16_utils.py`.
    *   Accept `block_desc` and `cur_op_desc` as parameters.
    *   Search `block_desc` for `cur_op_desc` and return its integer index.
    *   Return -1 if `cur_op_desc` is not found.

*   Implement `find_true_post_op` in `python/paddle/fluid/contrib/mixed_precision/fp16_utils.py`.
    *   Accept a list of `ops`, a `cur_op`, and a `var_name` string as parameters.
    *   Return a list of ops that appear after `cur_op` and use `var_name` as an input.
    *   Return None if no such ops exist.

*   Update `DistributedStrategy` in `python/paddle/fluid/incubate/fleet/collective/__init__.py`.
    *   Add a boolean attribute `use_amp` with a default value of False.

*   Modify `CollectiveOptimizer` in `python/paddle/fluid/incubate/fleet/collective/__init__.py`.
    *   In `__init__`, raise `ValueError` if `strategy.recompute_checkpoints` is not a list.
    *   In `minimize`, raise `ValueError` if:
        *   `forward_recompute` is True and `recompute_checkpoints` is an empty list.
        *   `forward_recompute` is True and the wrapped optimizer is already a `RecomputeOptimizer`.
        *   `use_amp` is True and the wrapped optimizer is already an `OptimizerWithMixedPrecision`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.