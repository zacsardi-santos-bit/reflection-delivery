I'm working on a memory pool configurator for hybrid sliding window attention models.

*   SWAChunkCapPoolConfigurator must be importable from sglang.srt.model_executor.pool_configurator.

*   create_memory_pool_configurator must return an instance of SWAChunkCapPoolConfigurator when the model is a hybrid SWA model and server_args.max_running_requests is not None. It must NOT return an instance of SWAChunkCapPoolConfigurator when server_args.max_running_requests is None.

*   SWAChunkCapPoolConfigurator.calculate_pool_sizes(available_bytes, page_size) must return a MemoryPoolConfig whose swa_max_total_num_tokens field is computed as ceil_align(swa_cap, page_size), where swa_cap depends on disaggregation mode as described in the remaining requirements.

*   The per-request token cost is: trailing_tokens + decode_alloc. trailing_tokens = sliding_window_size + (SGLANG_SWA_EVICTION_INTERVAL * (speculative_num_draft_tokens or 1)) + page_size. When there is no speculative decoding, speculative_num_draft_tokens is treated as 1. decode_alloc for non-speculative requests equals page_size.

*   When speculative decoding is active and overlap scheduling is DISABLED (spec-v1), decode_alloc per request = max(ceil_align(speculative_num_steps + ceil_align(speculative_eagle_topk, page_size), page_size) * 2, ceil_align(max_speculative_num_draft_tokens, page_size)). When speculative decoding is active and overlap scheduling is ENABLED (spec-v2), decode_alloc per request = 2 * max(speculative_num_steps * speculative_eagle_topk, max_speculative_num_draft_tokens).

*   For disaggregation_mode 'decode': the global prefill budget is omitted entirely. swa_cap = per_request_tokens * (max_running_requests // dp_size) + (sliding_window_size + page_size) * disaggregation_decode_extra_slots.

*   For non-decode modes (including disaggregation_mode 'prefill' and the standard case): global_prefill_tokens = chunks_in_flight * chunked_prefill_size + page_size, where chunks_in_flight is 2 when overlap scheduling is enabled and 1 when it is disabled. swa_cap = per_request_tokens * (max_running_requests // dp_size) + global_prefill_tokens.

*   The following specific numerical results must hold (with SGLANG_SWA_EVICTION_INTERVAL=4 in all cases): (a) spec-v1 EAGLE with sliding_window_size=8, page_size=4, max_running_requests=2, dp_size=1, speculative_num_steps=3, speculative_eagle_topk=2, speculative_num_draft_tokens=5, chunked_prefill_size=4, disable_overlap_schedule=True, disaggregation_mode='null' → swa_max_total_num_tokens == 104; (b) spec-v2 EAGLE with sliding_window_size=8, page_size=1, max_running_requests=2, dp_size=1, speculative_num_steps=3, speculative_eagle_topk=2, speculative_num_draft_tokens=5, chunked_prefill_size=4, disable_overlap_schedule=False, disaggregation_mode='null' → swa_max_total_num_tokens == 91; (c) disagg decode with sliding_window_size=4, page_size=1, max_running_requests=10, dp_size=1, no speculative decoding, disaggregation_decode_extra_slots=0 → swa_max_total_num_tokens == 100; (d) disagg prefill with sliding_window_size=8, page_size=4, max_running_requests=2, dp_size=1, chunked_prefill_size=16, disable_overlap_schedule=False, no speculative decoding → swa_max_total_num_tokens == 76; (e) disagg decode with extra slots: sliding_window_size=4, page_size=1, max_running_requests=10, dp_size=1, disaggregation_decode_extra_slots=2, no speculative decoding → swa_max_total_num_tokens == 110.

*   The total memory used by the computed pool sizes must not exceed the available_bytes argument passed to calculate_pool_sizes (memory safety guarantee).

*   The SGLANG_SWA_EVICTION_INTERVAL environment variable in sglang.srt.environ must support an .override(value) context manager method to allow test-time overrides of the eviction interval value. Its default integer value when not overridden should be 128.


*   Interface details: Type: Class
Name: SWAChunkCapPoolConfigurator
Location: python/sglang/srt/model_executor/pool_configurator.py
Description: A hybrid sliding window attention memory pool configurator that sizes the SWA pool from a fixed per-request token cap when max_running_requests is explicitly set. Extends HybridSWAPoolConfigurator. Must be importable from sglang.srt.model_executor.pool_configurator.
Signature: calculate_pool_sizes(available_bytes: int, page_size: int) -> MemoryPoolConfig

Type: Function
Name: create_memory_pool_configurator
Location: python/sglang/srt/model_executor/pool_configurator.py
Description: Factory function that selects and instantiates the appropriate memory pool configurator based on model architecture. Must return a SWAChunkCapPoolConfigurator instance when the model is hybrid SWA and server_args.max_running_requests is not None (and applicable conditions are met). Must NOT return a SWAChunkCapPoolConfigurator when server_args.max_running_requests is None.
Signature: create_memory_pool_configurator(mr) -> MemoryPoolConfigurator


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.