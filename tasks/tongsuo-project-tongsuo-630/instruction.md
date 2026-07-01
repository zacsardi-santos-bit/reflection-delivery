Implement a mechanism to correctly manage SSL buffer freeing in pipelined connections. Ensure that buffers are not freed when there is any unread or partially received data. Additionally, create a helper function to load and initialize a pipeline-capable test engine.

*   Implement the `load_dasync` function in `test/helpers/ssltestlib.c` and declare it in `test/helpers/ssltestlib.h`.
    *   Use `ENGINE_by_id("dasync")`, `ENGINE_init()`, and `ENGINE_register_ciphers()` to load and initialize the "dasync" engine.
    *   Return the initialized ENGINE pointer on success, or NULL if any step fails.
    *   Conditionally compile this function only when both TLS1.2 and dynamic engine support are enabled (i.e., when `OPENSSL_NO_TLS1_2` and `OPENSSL_NO_DYNAMIC_ENGINE` are both undefined). Otherwise, return NULL unconditionally.

*   Update `SSL_free_buffers` to ensure it returns 0 (false) in the following scenarios:
    *   When there is unread application data remaining after a partial read.
    *   When the SSL read buffer contains only a partial record header (less than the full SSL3 record header length).
    *   When the SSL read buffer contains a complete record header but the record body has not yet been received.
    *   When the SSL read buffer contains a complete record header plus a partial record body.
    *   When pipelining is enabled and a second pipelined record is only partially received, even if the first record was successfully read.

*   Ensure `SSL_read_ex` succeeds and returns data from the first complete pipelined record in pipelining mode, while `SSL_free_buffers` called immediately after returns 0 if a partial second record is available.

*   Ensure `SSL_set_split_send_fragment` returns 0 (false/failure) when the requested fragment size exceeds `SSL3_RT_MAX_PLAIN_LENGTH`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.