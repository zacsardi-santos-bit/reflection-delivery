Implement the specified changes to the C byte buffer library to resolve API inconsistencies. Update function signatures, argument orders, and naming conventions to align with the "destination first" pattern and ensure correct data type associations.

*   Update Buffer Initialization Functions:
    *   Modify `aws_byte_buf_init` to accept the output buffer pointer as the first argument, the allocator as the second, and the capacity as the third.
    *   Modify `aws_byte_buf_init_copy` to accept the destination buffer pointer as the first argument, the allocator as the second, and the source buffer pointer as the third. Ensure that if the source buffer pointer is NULL, the destination's allocator is set to NULL, capacity to 0, and len to 0. Derive destination capacity from the source's len field.

*   Implement New Buffer Utility:
    *   Create `aws_byte_buf_from_empty_array` to initialize a `struct aws_byte_buf` over a pre-existing byte array, setting capacity to the array size and len to 0.

*   Update Write Functions:
    *   Rename and modify the write functions to accept a `struct aws_byte_buf` pointer as the destination:
        *   `aws_byte_buf_write`
        *   `aws_byte_buf_write_u8`
        *   `aws_byte_buf_write_be16`
        *   `aws_byte_buf_write_be32`
        *   `aws_byte_buf_write_be64`
        *   `aws_byte_buf_write_from_whole_buffer` (accept source by value)
        *   `aws_byte_buf_write_from_whole_string`
    *   Ensure these functions return true on success and false if the buffer lacks sufficient capacity, leaving the buffer unmodified on failure.

*   Update Encoding and Decoding Functions:
    *   Modify `aws_hex_encode`, `aws_hex_decode`, `aws_base64_encode`, `aws_base64_decode`, and `aws_base64_compute_decoded_len` to accept a `const struct aws_byte_cursor` pointer as the input parameter.

*   Update String Splitting Functions:
    *   Rename and modify `aws_byte_buf_split_on_char` to `aws_byte_cursor_split_on_char` and `aws_byte_buf_split_on_char_n` to `aws_byte_cursor_split_on_char_n`.
    *   Accept a `const struct aws_byte_cursor` pointer as the first argument.
    *   Change `aws_byte_cursor_split_on_char_n` to accept the maximum number of splits as the third argument.

*   Update Integer Serialization Helpers:
    *   Modify `aws_write_u64`, `aws_write_u32`, `aws_write_u24`, and `aws_write_u16` to accept the integer value as the first argument and the destination byte buffer pointer as the second.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.