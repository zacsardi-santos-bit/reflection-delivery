## Description

The SDK library used for communicating with the management and client APIs has been updated to a new version that introduces several breaking changes. The existing code in the test suite and integration layer needs to be updated to match the new API surface.

The key changes required are:

- **Client constructors now require an extra parameter**: When creating a management or client API client, callers previously passed two arguments (a URL and a certificate pool). The updated SDK requires a third argument, which callers may leave unset when no special configuration is needed.

- **Token access moved to a method**: The authentication session object previously exposed the session token as a direct field. The updated type exposes it through a method call instead.

- **Authentication event callback type changed**: Callbacks registered for authentication state changes (full authentication, partial authentication, and unauthenticated state) previously received a session-detail model generated from the API spec. They now receive a dedicated session type defined in the updated networking SDK.

- **Internal session field renamed**: The field on the controller client used to store and atomically access the current API session has been renamed. Code that reads the old field name will not compile.

- **Error wrapping for certificate auth**: Errors from certificate-based authentication should be wrapped to surface richer diagnostic information from the underlying HTTP response.

## Expected Behavior

- All existing authentication flows (username/password, certificate, and JWT) should continue to work after the update.
- Authentication state events should fire correctly and deliver the new session type to listeners.
- The session token should be non-empty after successful authentication.

## Why This Matters

Keeping the SDK dependency current is necessary to support new authentication mechanisms, including OIDC-based token refresh, which requires the new configuration parameter on the client constructors. Without this update, the codebase is blocked from adopting these capabilities.
