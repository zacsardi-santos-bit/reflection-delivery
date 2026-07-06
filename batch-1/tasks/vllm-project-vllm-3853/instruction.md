Implement a "chunked prefill" mode in the vLLM scheduler to allow long prompts to be processed in smaller pieces over multiple scheduling rounds. This will enable decode steps for other requests to run concurrently, improving efficiency and reducing latency.

*   Update `SchedulerConfig` in `vllm/config.py`:
    *   Add `enable_chunked_prefill` as a boolean keyword parameter (default False).
    *   Allow configurations where `max_num_batched_tokens` is less than `max_model_len` when `enable_chunked_prefill=True`.

*   Modify `SequenceGroup` in `vllm/sequence.py`:
    *   Implement `is_prefill()` method to return True if any prompt tokens are uncomputed.
    *   Implement `update_num_computed_tokens(num_new_computed_tokens: int)` to accumulate computed tokens and transition from prefill to decode stage.
    *   Implement `get_num_uncomputed_tokens()` to return the number of tokens yet to be computed.

*   Modify `SequenceData` and `Sequence` in `vllm/sequence.py`:
    *   Implement `reset_state_for_recompute()` to reset the computed token count and return to the prefill stage.

*   Update `SchedulingBudget` in `vllm/core/scheduler.py`:
    *   Accept only `token_budget` and `max_num_seqs` in the constructor.
    *   Implement `can_schedule(num_new_tokens: int, num_new_seqs: int) -> bool` to check if new tokens and sequences fit within the budget.
    *   Implement `remaining_token_budget() -> int` to return the remaining token budget.
    *   Implement idempotent methods `add_num_batched_tokens(req_id: str, num_batched_tokens: int)` and `subtract_num_batched_tokens(req_id: str, num_batched_tokens: int)`.
    *   Implement idempotent methods `add_num_seqs(req_id: str, num_curr_seqs: int)` and `subtract_num_seqs(req_id: str, num_curr_seqs: int)`.
    *   Expose `num_batched_tokens` and `num_curr_seqs` as readable properties.

*   Update scheduling methods in `vllm/core/scheduler.py`:
    *   Rename `_schedule_decodes` to `_schedule_running` and update its return type to include `decode_seq_groups` and `prefill_seq_groups`.
    *   Modify `_allocate_and_set_running` to accept `num_new_tokens` as an additional parameter.
    *   Update `_schedule_swapped` to replace `seq_groups` with `decode_seq_groups` and `prefill_seq_groups`.

*   Implement chunked prefill behavior when `enable_chunked_prefill=True`:
    *   Split long prompts into chunks bounded by `max_num_batched_tokens`.
    *   Prioritize decode requests over new prefill requests, and in-progress chunked prefills over new prefills.
    *   Preempt single-sequence requests by recompute when necessary.
    *   Reject requests exceeding `max_model_len` and report them in `SchedulerOutputs.ignored_seq_groups`.
    *   Enforce `max_num_seqs` even if the token budget is not fully utilized.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.