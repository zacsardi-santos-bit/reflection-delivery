Implement OAuth2 authorization code flow support for remote agents in the authentication system. Allow users to specify OAuth2 configurations in their remote agent configuration files, validate URLs, manage token lifecycle, and optimize credential exposure during agent card fetching.

*   Extend the `OAuth2AuthConfig` type in `packages/core/src/agents/auth-provider/types.ts`:
    *   Include optional string fields: `authorization_url` and `token_url`.

*   Implement the `OAuth2AuthProvider` class in `packages/core/src/agents/auth-provider/oauth2-provider.ts`:
    *   Constructor signature: `(config: OAuth2AuthConfig, agentName: string, agentCard?: AgentCard, agentCardUrl?: string)`.
    *   Ensure the `type` property is always `'oauth2'`.
    *   Implement `initialize(): Promise<void>`:
        *   Load stored tokens using `MCPOAuthTokenStorage`.
        *   Cache valid tokens; resolve agent cards if URLs are missing.
    *   Implement `headers(): Promise<Record<string, string>>`:
        *   Return authorization headers with a valid token.
        *   Refresh expired tokens or initiate PKCE flow if necessary.
        *   Handle user consent and error scenarios appropriately.
    *   Implement `shouldRetryWithHeaders(currentHeaders, response): Promise<Record<string, string> | undefined>`:
        *   Handle retries for 401/403 responses, with a max of 2 retries.

*   Update `A2AAuthProviderFactory` in `packages/core/src/agents/auth-provider/factory.ts`:
    *   Modify `create()` to accept an options object with an optional `agentCardUrl`.
    *   Instantiate and initialize `OAuth2AuthProvider` for `authConfig.type === 'oauth2'`.

*   Modify `parseAgentMarkdown` in `packages/core/src/agents/agentLoader.ts`:
    *   Accept `auth.type 'oauth2'` with optional fields: `client_id`, `client_secret`, `scopes`, `authorization_url`, `token_url`.
    *   Validate URLs and throw errors for invalid ones.

*   Update `markdownToAgentDefinition` in `packages/core/src/agents/agentLoader.ts`:
    *   Preserve OAuth2 auth configuration fields in the agent definition.

*   Ensure `AgentRegistry` and `RemoteAgentInvocation` pass `agentCardUrl` to `A2AAuthProviderFactory.create`.

*   Implement a custom fetch wrapper in `A2AClientManager.loadAgent` to handle unauthenticated and authenticated fetch attempts for agent card resolution.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.