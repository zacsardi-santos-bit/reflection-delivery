## Description

When Quarkus gRPC services are configured to share the same HTTP server as REST endpoints, developers currently have no way to authenticate gRPC requests using Quarkus's built-in HTTP authentication mechanisms unless they implement a custom authentication adapter specifically for gRPC. This forces unnecessary boilerplate: any developer who wants to use basic authentication, bearer tokens, or certificate-based authentication with their gRPC services on the shared server must write and register a custom bridge class.

## Expected Behavior

- gRPC services running on the shared HTTP server should be able to authenticate callers using the same authentication infrastructure already configured for REST endpoints — basic auth, bearer tokens, certificate-based auth, etc.
- Role-based access control annotations on gRPC service methods should be enforced using the identity resolved by the HTTP authenticator.
- This should work in both eager authentication mode (the default) and lazy (non-proactive) authentication mode.
- Existing custom authentication adapter implementations should continue to work with the same priority they have today.

## Why This Matters

Developers who mix REST and gRPC endpoints in the same application should not need two different authentication code paths. Unifying authentication for gRPC with the standard HTTP authentication infrastructure removes boilerplate, reduces the chance of security misconfigurations, and makes gRPC services behave consistently with REST endpoints when it comes to securing access.
