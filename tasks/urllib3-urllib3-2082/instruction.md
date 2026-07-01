Implement a mechanism to control TLS cipher selection in the library. Introduce a flag to determine whether to use the platform's SSL library defaults or the library's hardcoded cipher list. Reorganize existing SSL unit tests into a class for easier reuse.

*   Add a module-level boolean attribute `USE_DEFAULT_SSLCONTEXT_CIPHERS` in `src/urllib3/util/ssl_.py`.
    *   Ensure it is accessible as `ssl_.USE_DEFAULT_SSLCONTEXT_CIPHERS`.
    *   Allow it to be patchable for testing purposes.

*   Update the `create_urllib3_context()` function in `src/urllib3/util/ssl_.py`:
    *   Signature: `create_urllib3_context(ssl_version=None, cert_reqs=None, options=None, ciphers=None) -> SSLContext`.
    *   If `ciphers` is `None` and `USE_DEFAULT_SSLCONTEXT_CIPHERS` is `True`, do not call `set_ciphers()` on the SSL context.
    *   If `ciphers` is `None` and `USE_DEFAULT_SSLCONTEXT_CIPHERS` is `False`, call `set_ciphers()` with `DEFAULT_CIPHERS`.
    *   If `ciphers` is not `None`, always call `set_ciphers()` with the provided `ciphers`.

*   Reorganize SSL unit tests in `test/test_ssl.py`:
    *   Create a class named `TestSSL` to group all existing SSL utility unit tests.
    *   Ensure the class is importable as `from test.test_ssl import TestSSL`.
    *   Make the test suite reusable for alternative SSL backend implementations, such as in `test/contrib/test_pyopenssl.py`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.