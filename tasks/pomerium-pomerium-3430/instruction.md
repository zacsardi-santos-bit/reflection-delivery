Implement a utility function to determine the client's real IP address from an HTTP request, considering scenarios where Pomerium runs behind an Envoy proxy. Ensure the function checks for a specific header and falls back to other methods if necessary.

* Implement the `GetClientIPAddress` function in the `internal/httputil` package.
    * The function must accept a single parameter: `r *http.Request`.
    * The function must return a string representing the client's IP address.
* Ensure the function follows these rules for determining the IP address:
    * If the `X-Envoy-External-Address` header is present, return its value.
    * If the header is absent, parse the IP from `r.RemoteAddr`, stripping any port information.
        * Example: If `RemoteAddr` is "127.0.0.2:1234", return "127.0.0.2".
    * If `RemoteAddr` is not set or is unparseable, return "127.0.0.1" as the default loopback address.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.