Implement the integration of the expert parallel load balancing (EPLB) system into the GPU model runner for vLLM's V1 worker stack. Ensure that the system is properly initialized and utilized during model loading and inference, with specific behavior for real and dummy weights.

*   Update `GPUModelRunner.load_model` to:
    *   Accept a `load_dummy_weights` parameter (default `False`).
    *   Set `load_config.load_format` to `'dummy'` when `load_dummy_weights=True`.
    *   Always create an `EplbState` instance and assign it to `self.eplb_state`.
    *   When `load_dummy_weights=False`:
        *   Call `prepare_communication_buffer_for_model(model)`.
        *   Register the model with `self.eplb_state.add_model(model, model_config)`.
        *   Start the background loop with `self.eplb_state.start_async_loop()`.
    *   When `load_dummy_weights=True`, skip communication buffer preparation and EPLB state registration.

*   Implement `GPUModelRunner.setup_eplb_from_mapping`:
    *   Accept `expanded_physical_to_logical` and `num_valid_physical_experts` as parameters.
    *   Create a new `EplbState` using `EplbState.from_mapping` with the necessary keyword arguments and assign it to `self.eplb_state`.

*   Modify `GPUModelRunner.sample_tokens`:
    *   Call `self.eplb.step(...)` after `self.postprocess(...)` if `self.is_last_pp_rank` is `False`.
    *   Return `None` when `self.is_last_pp_rank` is `False`.

*   Ensure `EplbState` in `vllm/v1/worker/gpu/eplb_utils.py` supports:
    *   Constructor with `parallel_config` and `device`.
    *   Methods: `add_model`, `step`, `start_async_loop`.
    *   Classmethod `from_mapping` with required keyword arguments.

*   Ensure `EPLBController` in `vllm/v1/worker/gpu/eplb_utils.py` supports:
    *   Constructor with `parallel_config` and `device`.
    *   `step` method.

*   Implement `is_mixture_of_experts` function in `vllm/v1/worker/gpu/eplb_utils.py` to return a boolean indicating if a model is a mixture-of-experts architecture.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.