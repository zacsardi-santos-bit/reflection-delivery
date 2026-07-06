Implement a function to perform MAP (maximum a posteriori) estimation over discrete latent variables in models using Pyro's funsor-based contrib backend. Ensure it works across various model structures and in hybrid inference settings with both continuous and discrete latents. Fix the ReplayMessenger to support this functionality.

*   Implement the `infer_discrete` function in `pyro/contrib/funsor/infer/discrete.py`.
    *   Accept a callable `model` and an optional `first_available_dim` parameter (default `None`).
    *   Return a callable that, when invoked, runs the model and sets discrete latent variables to their MAP values.
*   Ensure the returned callable:
    *   Produces a result trace with a `log_prob_sum()` equal to the maximum log probability across all discrete configurations.
    *   Works with models having single discrete latents, sequential dependencies, plate-vectorized latents, and combinations thereof.
    *   Maintains the same site names as the original model when conditioned on sampled continuous latents.
    *   Keeps continuous latent sites free of discrete enumeration variable dependencies in the result trace.
*   Modify `ReplayMessenger` in `pyro/contrib/funsor/handlers/replay_messenger.py`:
    *   Remove the early-exit behavior triggered by `msg['is_observed']` to allow replaying against traces with observed sites.
*   Export `infer_discrete` from `pyro/contrib/funsor/infer/__init__.py` to make it accessible as `infer.infer_discrete`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.