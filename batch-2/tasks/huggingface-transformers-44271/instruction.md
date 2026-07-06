Implement a new adaptive learning rate scheduler, `GreedyLR`, in the transformers library that can both increase and decrease the learning rate based on training metrics. Ensure it is configurable, supports serialization, and integrates with the existing training framework.

*   Implement the `GreedyLR` class in `src/transformers/optimization.py`:
    *   Validate constructor parameters:
        *   Raise `ValueError` if `factor` >= 1.0, `mode` is not 'min' or 'max', or `threshold_mode` is not 'rel' or 'abs'.
        *   Raise `TypeError` if `optimizer` is not an instance of `torch.optim.Optimizer`.
    *   Initialize attributes:
        *   `best` to `float('inf')` for 'min' mode and `float('-inf')` for 'max' mode.
        *   `min_lrs` and `max_lrs` as lists with one entry per optimizer parameter group.
    *   Implement `step(metrics: float, epoch: int | None = None) -> None`:
        *   Decrease learning rate by multiplying by `factor` after more than `patience` consecutive non-improving steps.
        *   Increase learning rate by dividing by `factor` after more than `patience` consecutive improving steps.
        *   Respect cooldown and warmup periods.
    *   Implement `is_better(current: float, best: float) -> bool` to determine if the current metric is better than the best.
    *   Implement `get_last_lr() -> list[float]` to return current learning rates.
    *   Implement `state_dict() -> dict` and `load_state_dict(state_dict: dict) -> None` for serialization support.
    *   Implement automatic reset of learning rate after it has been stuck at `min_lr` for `reset_start` consecutive attempts.

*   Implement the `StreamingAverage` class in `src/transformers/optimization.py`:
    *   Maintain a rolling window of metric values and compute their average.
    *   Implement `streamavg(value: float) -> float` to add a value and return the current average.
    *   Implement `state_dict() -> dict` and `load_state_dict(state_dict: dict) -> None` for serialization.

*   Create `get_greedy_schedule(optimizer: Optimizer, **kwargs) -> GreedyLR` in `src/transformers/optimization.py` to return a `GreedyLR` instance.

*   Update `SchedulerType` enum in `src/transformers/trainer_utils.py` to include `GREEDY = 'greedy'`.

*   Update `TYPE_TO_SCHEDULER_FUNCTION` in `src/transformers/optimization.py` to map `SchedulerType.GREEDY` to `get_greedy_schedule`.

*   Modify `get_scheduler` in `src/transformers/optimization.py` to handle `SchedulerType.GREEDY` without requiring `num_warmup_steps`.

*   Export `GreedyLR` and `get_greedy_schedule` from the top-level `transformers` package.

*   Integrate `GreedyLR` into `src/transformers/trainer.py`:
    *   Ensure it is recognized in `isinstance` checks for `ReduceLROnPlateau`.
    *   Step the scheduler with evaluation metrics after each evaluation.

*   Update `TrainingArguments` in `src/transformers/training_args.py`:
    *   Validate `lr_scheduler_type=SchedulerType.GREEDY` with appropriate evaluation strategy.
    *   Auto-set `metric_for_best_model` when necessary.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.