Update the Boa JavaScript engine to support resizable array buffers by modifying the internal function responsible for memory allocation. Introduce an optional parameter for maximum byte length, allowing buffers to grow dynamically up to a specified limit.

*   Modify the `create_byte_data_block` function in `core/engine/src/builtins/array_buffer/mod.rs`:
    *   Add a new parameter `max_byte_length` of type `Option<u64>` between the existing `size` and `context` parameters.
    *   Ensure the function signature is: `create_byte_data_block(size: u64, max_byte_length: Option<u64>, context: &mut Context) -> JsResult<Vec<u8>>`.

*   Implement behavior when `max_byte_length` is `None`:
    *   Maintain current behavior for valid sizes: return `Ok` for allocations like 100 bytes.
    *   Maintain current behavior for unreasonably large sizes: return `Err` for allocations like `u64::MAX`.

*   Ensure the function supports the creation of resizable buffers with a defined upper limit when `max_byte_length` is provided.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.