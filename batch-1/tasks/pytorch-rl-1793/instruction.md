Implement a new loss module for discrete-action Implicit Q-Learning (IQL) in the `torchrl.objectives` package. Ensure that this module can train an actor network, a value network, and one or more Q-value networks with independent loss terms. Provide a runnable example script demonstrating its use.

*   Implement the `DiscreteIQLLoss` class in `torchrl/objectives/iql.py` or `torchrl/objectives/discrete_iql.py` with the following constructor signature:
    *   `__init__(actor_network, qvalue_network, value_network, num_qvalue_nets: int = 2, temperature: float = 1.0, expectile: float = 0.5, loss_function: str = "smooth_l1", separate_losses: bool = False) -> None`
*   Ensure `DiscreteIQLLoss` is importable via `from torchrl.objectives import DiscreteIQLLoss`.
*   Implement the `forward` method to:
    *   Return a `TensorDict` with keys: 'loss_actor', 'loss_qvalue', 'loss_value', 'entropy'.
    *   Write the priority value (key: `tensor_keys.priority`, default 'td_error') into the input `TensorDict`.
    *   Emit a `UserWarning` with the phrase 'No target network updater' if no target network updater is associated.
*   Ensure loss terms are independent:
    *   `loss_actor` should only update `actor_network_params`.
    *   `loss_value` should only update `value_network_params`.
    *   `loss_qvalue` should only update `qvalue_network_params`.
*   Implement handling of shared layers:
    *   When `separate_losses=True`, prevent gradient flow from `loss_actor` into shared layers.
    *   When `separate_losses=False`, allow gradient flow through shared layers.
*   Ensure non-target parameters receive non-zero gradients, while target parameters receive zero gradients after backpropagation.
*   Implement `make_value_estimator` to:
    *   Raise `NotImplementedError` for `ValueEstimators.GAE` and `ValueEstimators.VTrace`.
    *   Support `ValueEstimators.TD0`, `ValueEstimators.TD1`, and `ValueEstimators.TDLambda`.
*   Implement serialization methods:
    *   `state_dict()` and `load_state_dict(state_dict: dict) -> None`.
*   Support calling with keyword arguments:
    *   Return a tuple: `(loss_actor, loss_qvalue, loss_value, entropy)`.
    *   Implement `select_out_keys(*keys) -> None` to restrict output keys.
    *   Implement `set_keys(**kwargs) -> None` to configure tensor key mappings.
*   Ensure compatibility with multi-step reward processing:
    *   When `n_steps=0`, losses should be numerically close to those without multi-step.
    *   When `n_steps > 0`, losses should differ.
*   Create a runnable example script at `examples/iql/discrete_iql.py`:
    *   Accept `hydra/omegaconf` configuration options including `collector.total_frames`, `optim.batch_size`, `collector.frames_per_batch`, `env.train_num_envs`, `optim.device`, `collector.device`, `logger.mode`, and `logger.backend`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.