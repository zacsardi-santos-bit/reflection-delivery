Optimize the weight loading phase for large mixture-of-experts models by implementing a filtering mechanism to skip non-local expert weights. Ensure that only the necessary expert weights are loaded based on the rank's assigned subset of experts, thereby reducing storage I/O.

* Implement `parse_expert_id` to:
    * Extract and return the integer expert ID from a weight tensor name containing the pattern '.experts.<N>.'.
    * Return `None` for shared experts, attention weights, embeddings, layernorm weights, and fused 3D expert tensors without a numeric ID.
    * Return expert ID 0 as integer 0.
    * Handle quantization scale tensors by returning the numeric expert ID if present.

* Implement `compute_local_expert_ids` to:
    * Return `None` when `ep_size` is 1 or less, indicating no filtering is needed.
    * Assign contiguous blocks of expert IDs to each rank for default placement:
        * Calculate `k = num_experts/ep_size`.
        * Assign experts [r*k, (r+1)*k) to rank r, with earlier ranks receiving one additional expert if uneven.
    * Assign experts in a round-robin fashion for `placement='round_robin'`:
        * Assign expert i to rank (i % ep_size).
        * Ensure the union of all ranks' expert sets equals the full range [0, num_experts) with no overlaps.

* Implement `should_skip_weight` to:
    * Return `False` when `local_expert_ids` is `None`.
    * Return `False` for non-expert weight names, shared expert weights, and fused 3D expert tensor names.
    * Return `True` for expert weight names not in `local_expert_ids`.

* Update `safetensors_weights_iterator` to:
    * Accept a new keyword argument `local_expert_ids` (default `None`).
    * Yield all weights when `local_expert_ids` is `None`.
    * Skip non-local expert weights when `local_expert_ids` is provided.
    * Always yield dense, shared, and fused-3D expert weights, regardless of `local_expert_ids`.
    * Ensure tensor values for non-skipped weights are identical to those without filtering.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.