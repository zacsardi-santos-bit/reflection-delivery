## Description

When running a hybrid sliding window attention model with a known upper bound on the number of concurrent requests, there is currently no way to compute a tight, accurate token capacity for the sliding window cache. The existing memory pool configurators either use a ratio-based heuristic or don't account for the precise per-request footprint. This leads to wasted memory (over-provisioning) or potential out-of-memory errors (under-provisioning), especially in production deployments.

## Expected Behavior

- When a maximum number of running requests is explicitly specified, the system should automatically select a configurator that computes the sliding window cache capacity from the actual worst-case per-request token footprint.
- The per-request footprint should account for: the sliding window size, an eviction buffer proportional to the eviction interval, page alignment, and the decode allocation needed for speculative decoding.
- For speculative decoding without overlap scheduling, the decode allocation should be computed from the steps and draft token counts with page alignment. With overlap scheduling enabled, the allocation should double to account for two simultaneous decode passes.
- Disaggregated decode-only nodes should skip the global prefill budget entirely when computing the cap.
- Disaggregated decode nodes with in-transfer pre-allocation slots should add a reduced per-slot cost (window size plus page size only, without the full eviction buffer).
- Nodes running in prefill or standard mode should include a global prefill budget covering the in-flight chunked prefill tokens (one chunk without overlap, two chunks with overlap scheduling).

## Why This Matters

This enables precise, safe memory allocation for sliding window attention caches in bounded-concurrency deployments, eliminating guesswork from the sizing formula and ensuring no memory is wasted or incorrectly exhausted when running with speculative decoding or disaggregated architectures.
