Add a method to the `AsyncDecoder` trait that allows decoder implementations to specify the minimum number of additional samples they need enqueued beyond the current request. Ensure the default behavior requires zero extra samples, allowing existing decoders to function without modification.

*   Update the `AsyncDecoder` trait in `crates/utils/re_video/src/decode/mod.rs`:
    *   Implement a new method named `min_num_samples_to_enqueue_ahead`.
    *   Ensure the method signature is `fn min_num_samples_to_enqueue_ahead(&self) -> usize`.
    *   Provide a default implementation for `min_num_samples_to_enqueue_ahead` that returns `0`.
*   Ensure that any type implementing `AsyncDecoder` without overriding `min_num_samples_to_enqueue_ahead` returns `0` when the method is called.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.