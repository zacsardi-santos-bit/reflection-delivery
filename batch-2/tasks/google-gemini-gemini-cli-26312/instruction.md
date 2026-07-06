Implement a dynamic OAuth token provider for MCP transports that fetches and refreshes tokens on demand. Ensure the provider caches tokens with known expiration times and re-fetches tokens without expiration timestamps on each request.

*   Update the `createTransport` function in `packages/core/src/tools/mcp-client.ts`:
    *   Use `MCPOAuthTokenStorage` to check for stored OAuth credentials with `getCredentials(serverName)`.
    *   If credentials exist, attach a `DynamicStoredOAuthProvider` instance to the transport's `_authProvider`.
    *   Ensure `_authProvider.tokens()` returns a valid `access_token`.
    *   If OAuth is enabled but credentials are missing, log an info-level message instructing the user to authenticate.

*   Implement `DynamicStoredOAuthProvider` in `packages/core/src/mcp/stored-token-provider.ts`:
    *   Constructor must accept `serverName: string` and `serverConfig: MCPServerConfig`.
    *   Internally instantiate `MCPOAuthProvider` and use `getValidTokenWithMetadata` to resolve the token.
    *   Implement an async `tokens()` method that:
        *   Returns an object with `access_token` and optionally `expires_in`.
        *   Caches tokens with `expiresAt` in memory to avoid redundant lookups.
        *   Re-fetches tokens without `expiresAt` on each call.

*   Modify `MCPOAuthProvider` in `packages/core/src/mcp/oauth-provider.ts`:
    *   Implement `getValidTokenWithMetadata(serverName: string, config: object)` method.
    *   Return an object with `accessToken`, `tokenType`, and optionally `expiresAt`.
    *   Return `null` if no valid credentials are found or refresh fails.

*   Ensure `DynamicStoredOAuthProvider.tokens()`:
    *   Returns `expires_in` as seconds remaining when `expiresAt` is available.
    *   Does not cache tokens without `expiresAt`, invoking `getValidTokenWithMetadata` on each call.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.