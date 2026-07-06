Implement dedicated configuration classes for optimizers SGD, Adam, and Lamb in the ONNX Runtime training module. Ensure these configurations have sensible default values and validate inputs early. Provide a base configuration class to handle shared validation logic.

*   Implement `optim.config._OptimizerConfig` class:
    *   Accept `name` (string), `params` (list of dicts), and `defaults` (dict) as keyword arguments.
    *   Expose `name`, `lr`, `defaults`, and `params` attributes.
    *   Validate `name` is a string matching 'SGDOptimizer', 'AdamOptimizer', or 'LambOptimizer'.
    *   Ensure `defaults` is a dict with a non-negative `lr`.
    *   Validate `params` is a list of dicts, each containing a 'params' key.
    *   Enforce a 1:1 mapping between hyperparameter keys in `defaults` and each `params` entry.
    *   Raise `AssertionError` on validation failures.

*   Implement `optim.SGDConfig` class:
    *   Accessible as `optim.SGDConfig`.
    *   Default `lr` is 0.001; `name` attribute is 'SGDOptimizer'.
    *   Accept optional `lr` keyword argument.
    *   Raise `AssertionError` with message "'params' must be an empty list for SGD optimizer" if `params` is non-empty.

*   Implement `optim.AdamConfig` class:
    *   Accessible as `optim.AdamConfig`.
    *   Default values: `lr=0.001`, `alpha=0.9`, `beta=0.999`, `lambda_coef=0.0`, `epsilon=1e-8`, `do_bias_correction=True`, `weight_decay_mode=AdamConfig.DecayMode.BEFORE_WEIGHT_UPDATE`.
    *   `name` attribute is 'AdamOptimizer'.
    *   Support optional `params` and keyword hyperparameter arguments.
    *   Expose inner enum class `DecayMode` with `BEFORE_WEIGHT_UPDATE` member.
    *   Raise `AssertionError` with message "'lr' is not supported inside params" if `lr` appears in any `params` entry.

*   Implement `optim.LambConfig` class:
    *   Accessible as `optim.LambConfig`.
    *   Default values: `lr=0.001`, `alpha=0.9`, `beta=0.999`, `lambda_coef=0.0`, `ratio_min=float('-inf')`, `ratio_max=float('inf')`, `epsilon=1e-6`, `do_bias_correction=True`.
    *   `name` attribute is 'LambOptimizer'.
    *   Support optional `params` and keyword hyperparameter arguments.
    *   Raise `AssertionError` with message "'lr' is not supported inside params" if `lr` appears in any `params` entry.

*   Ensure per-parameter hyperparameter values in `params` take precedence over global defaults for both `AdamConfig` and `LambConfig`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.