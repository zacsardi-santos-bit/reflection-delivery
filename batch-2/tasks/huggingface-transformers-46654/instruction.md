Update the `create_diffusion_decoder_attention_mask` method in the `DiffusionGemmaDecoderModel` to simplify the handling of attention masks for static caches. Ensure the function accepts the top-level configuration object and automatically manages unfilled cache slots.

*   Modify the `create_diffusion_decoder_attention_mask` method to accept `DiffusionGemmaConfig` as the `config` parameter.
    *   Ensure the method internally extracts necessary sub-configurations from `DiffusionGemmaConfig`.

*   Update the `decoder_attention_mask` parameter to accept a shape of `(batch_size, actual_input_length + canvas_length)`.
    *   `actual_input_length` should represent the number of tokens filled into the cache, not the maximum cache capacity.
    *   Users should not need to zero out unfilled cache positions.

*   Implement logic to handle different cache scenarios:
    *   If total input length does not exceed the sliding window:
        *   Return a `full_attention` mask with shape `(batch_size, 1, canvas_length, max_cache_len + canvas_length)`.
        *   Ensure non-zero count equals `(prefill_length + canvas_length) * canvas_length * batch_size`.
    *   If total input length exceeds the sliding window:
        *   Return a `full_attention` mask with shape `(batch_size, 1, canvas_length, max_cache_len + canvas_length)`.
        *   Return a `sliding_attention` mask with shape `(batch_size, 1, canvas_length, sliding_window_length + canvas_length)`.
        *   Ensure `sliding_attention` non-zero count equals `(sliding_window_length + canvas_length - 1) * batch_size * canvas_length`.

*   Adjust handling of left-padding in `decoder_attention_mask`:
    *   Reduce the non-zero count in `full_attention` by `left_padding_length` for each item with left-padding.

*   Remove or update validation that raises a `ValueError` for masks not matching `(batch_size, max_cache_len + canvas_length)`.
    *   Accept the new shape `(batch_size, actual_seq_len + canvas_length)`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.