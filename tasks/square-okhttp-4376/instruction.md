Implement preemptive proxy authentication in OkHttp to allow the proxy authenticator to supply credentials proactively, reducing unnecessary network round trips. Update the `RecordingOkAuthenticator` class to filter responses based on authentication schemes.

*   Update the `RecordingOkAuthenticator` class:
    *   Add a two-argument constructor: `RecordingOkAuthenticator(@Nullable String credential, @Nullable String scheme)`.
    *   Introduce a public nullable field `scheme` alongside the existing `credential` field.
    *   Modify the `authenticate()` method:
        *   If `scheme` is non-null, return null for any response whose challenge list does not contain a challenge with that scheme (case-insensitive).
        *   If `scheme` is null, respond to all challenges as before.
        *   If `credential` is null, return null.
        *   Otherwise, return a new request with the appropriate header set (Authorization for 401, Proxy-Authorization for 407).

*   Implement preemptive proxy authentication:
    *   Before sending the initial CONNECT request to establish an HTTPS tunnel through a proxy, invoke the configured proxy authenticator with a synthetic 407 Proxy Authentication Required response.
    *   Include a 'Proxy-Authenticate: OkHttp-Preemptive' header in the synthetic response, creating a single challenge with the scheme 'OkHttp-Preemptive'.
    *   If the proxy authenticator returns a non-null request in response to the preemptive challenge, use that request (which may include a Proxy-Authorization header) as the CONNECT request.
    *   If the proxy authenticator returns null, send the CONNECT request without a Proxy-Authorization header.

*   Handle reactive authentication:
    *   After a proxy responds to an unauthenticated or rejected CONNECT with a real 407 and challenge headers (e.g., 'Proxy-Authenticate: Basic'), call the proxy authenticator again with the real response.
    *   Ensure the order of challenge schemes presented to the authenticator is ['OkHttp-Preemptive', 'Basic'] when both preemptive and reactive calls occur.

*   Ensure that once the proxy tunnel is established (CONNECT succeeds), the subsequent origin-server GET request does not carry a Proxy-Authorization header. Proxy credentials should only be sent on CONNECT requests.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.