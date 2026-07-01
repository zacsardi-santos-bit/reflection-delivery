Implement a dynamic TLS root CA update mechanism for gRPC client connections in Hyperledger Fabric. Allow the use of a callback function to modify TLS configurations dynamically during each handshake attempt, enabling automatic recovery from initial connection failures due to mismatched root CAs.

*   Update the `NewConnection` method in `core/comm/client.go`:
    *   Accept an optional variadic parameter of type `TLSOption`, which is a function that modifies a `*tls.Config`.
    *   Ensure the variadic parameter is the third argument, following `address` and `serverNameOverride`.
    *   Maintain backward compatibility for existing calls with only two arguments.

*   Implement dynamic credential handling:
    *   Use a dynamic credential mechanism when TLS is enabled and TLS option callbacks are provided.
    *   Clone the base TLS config and apply each callback to the clone on every TLS handshake attempt.
    *   Transition the connection to a `connectivity.TransientFailure` state if the root CAs do not match the server's certificate.

*   Ensure connection recovery:
    *   Allow the connection to recover to a `connectivity.Ready` state without closing and reopening once the correct root CAs are provided and `ResetConnectBackoff()` is called.

*   Maintain existing behavior:
    *   Ensure connections without TLS enabled continue to use insecure transport.
    *   Ensure existing connection code without callbacks remains functional and unchanged.

*   Implement the `DynamicClientCredentials` struct in `core/comm/creds.go`:
    *   Implement the `grpc.credentials.TransportCredentials` interface.
    *   Include methods: `ClientHandshake`, `Info()`, `Clone()`, and `OverrideServerName()`.
    *   Ensure `ServerHandshake` returns a non-nil error since server-side handshakes are unsupported.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.