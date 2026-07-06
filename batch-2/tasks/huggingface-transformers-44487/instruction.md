Implement a new training callback, `KubeflowCallback`, to report model training progress and metrics to a Kubeflow server. Ensure the callback is automatically enabled in a Kubeflow environment without manual configuration. Modify `TrainingArguments` to append "kubeflow" to the `report_to` list when necessary.

*   Implement `KubeflowCallback` in `src/transformers/integrations/integration_utils.py`:
    *   Ensure `KubeflowCallback` is importable from `transformers.integrations.integration_utils`.
    *   Define `is_kubeflow_available()` to return `True` if the integration is available.
    *   Initialize instance attributes in `__init__`: `_initialized` (False), `_metrics` ({}), `_start_time` (None), `_last_update_time` (0.0), `_cached_token` (None), `_token_read_time` (0.0), `_ssl_context` (None), `_ssl_context_initialized` (False).
    *   Implement `on_train_begin(args, state, control, **kwargs)`:
        *   Do nothing if `state.is_world_process_zero` is `False`.
        *   Set `_initialized` to `True` and call `_update_status(progress_percent=0, force=True)` if `True`.
    *   Implement `on_step_end(args, state, control, **kwargs)`:
        *   Do nothing if `_initialized` is `False`.
        *   Call `_update_status(progress_percent, estimated_time_remaining, metrics)` with `progress_percent` capped at 99.
    *   Implement `on_log(args, state, control, logs=None, **kwargs)`:
        *   Store only numeric values from `logs` into `_metrics`.
    *   Implement `on_train_end(args, state, control, **kwargs)`:
        *   Call `_update_status(progress_percent=100, estimated_time_remaining=0, force=True)`.
    *   Implement `_update_status(progress_percent, force=False, estimated_time_remaining=None, metrics=None)`:
        *   Return `False` if `KUBEFLOW_TRAINER_SERVER_URL` is not set.
        *   Throttle calls unless `force` is `True`.
        *   Return `True` after a successful HTTP request using `urllib.request.urlopen`.
        *   Reuse `_ssl_context` if `_ssl_context_initialized` is `True`.
    *   Implement `_get_token()`:
        *   Read and cache the token from the path specified by `KUBEFLOW_TRAINER_SERVER_TOKEN`.
        *   Validate cache using `time.monotonic()` and update `_token_read_time`.

*   Modify `TrainingArguments` in `src/transformers/training_args.py`:
    *   Append "kubeflow" to `report_to` when `KUBEFLOW_TRAINER_SERVER_URL` is set.
    *   Ensure "kubeflow" is not added more than once, even if `report_to` is initially "none".

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.