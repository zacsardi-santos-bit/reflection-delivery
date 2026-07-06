Implement the ability to dynamically determine and apply HTTP security headers based on the connection type in the proxy server. Ensure that the HTTPS enforcement header is only sent over TLS-secured connections and provide a mechanism for operators to disable all default security headers if needed.

*   Update the `GetSetResponseHeaders` method in `config/options.go`:
    *   Accept a boolean parameter `requireStrictTransportSecurity`.
    *   Return a map of HTTP headers:
        *   If `requireStrictTransportSecurity` is false, return: `{"X-Frame-Options": "SAMEORIGIN", "X-XSS-Protection": "1; mode=block"}`.
        *   If `requireStrictTransportSecurity` is true, return: `{"Strict-Transport-Security": "max-age=31536000; includeSubDomains; preload", "X-Frame-Options": "SAMEORIGIN", "X-XSS-Protection": "1; mode=block"}`.
        *   If `SetResponseHeaders` contains `DisableHeaderKey`, return an empty map `{}`.
*   Ensure the `DisableHeaderKey` constant is exported from the `config` package:
    *   Use it as a key in `SetResponseHeaders` to disable all default security headers.
*   Modify the default `Options` returned by `NewDefaultOptions`:
    *   Do not pre-populate `SetResponseHeaders` with static security headers.
    *   Use `GetSetResponseHeaders` to determine headers dynamically.
*   Update the `buildMainHTTPConnectionManagerFilter` function in `config/envoyconfig/listeners.go`:
    *   Add a third boolean parameter `requireStrictTransportSecurity`.
    *   Pass this parameter to downstream components for correct header application.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.