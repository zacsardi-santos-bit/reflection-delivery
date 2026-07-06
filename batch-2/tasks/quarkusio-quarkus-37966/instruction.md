Implement a feature in a Quarkus application that allows gRPC services to authenticate using the same HTTP authentication mechanisms configured for REST endpoints. This should work seamlessly without requiring custom authentication adapters for gRPC, supporting both eager and lazy authentication modes.

*   Ensure gRPC services on a shared Vert.x HTTP server can authenticate using standard HTTP authentication infrastructure without custom GrpcSecurityMechanism beans.
*   Support both eager (proactive) and lazy (non-proactive) authentication modes:
    *   Enable lazy mode with the configuration property `quarkus.http.auth.proactive=false`.
*   Implement role-based access control for gRPC service methods:
    *   Deny requests lacking required roles, returning a gRPC status error.
    *   Permit requests with required roles, returning the expected response.
*   Maintain priority for custom GrpcSecurityMechanism beans:
    *   If a custom bean's `handles()` method returns true for request metadata, it should handle authentication.
*   If no custom GrpcSecurityMechanism handles the request and the server is not using a separate gRPC server:
    *   The security interceptor should fall back to the HTTP-level authenticator.
    *   Support both already-resolved identities and deferred identity resolution.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.