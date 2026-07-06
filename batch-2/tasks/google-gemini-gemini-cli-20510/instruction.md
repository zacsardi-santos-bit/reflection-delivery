Implement support for user-defined HTTP authentication configurations in remote agent files. Ensure the system respects these configurations, supports various authentication schemes, and handles errors appropriately.

*   Update the agent markdown parser:
    *   Accept HTTP auth configurations with a `scheme` field as any non-empty string.
    *   Allow a `value` field for passing raw credentials directly.
    *   Ensure parsing succeeds when these fields are present, including them in the resulting `auth` object.

*   Modify the `HttpAuthProvider` class in `packages/core/src/agents/auth-provider/http-provider.ts`:
    *   Accept a config object in the constructor matching the `HttpAuthConfig` type.
    *   Implement the `initialize()` method to resolve credentials from environment variables if prefixed with `$`.
    *   Implement the `headers()` method to return appropriate `Authorization` headers based on the scheme:
        *   Bearer: `{ Authorization: 'Bearer <resolvedToken>' }`
        *   Basic: `{ Authorization: 'Basic <base64(username:password)>' }`
        *   Other schemes: `{ Authorization: '<scheme> <value>' }`
    *   Implement `shouldRetryWithHeaders(req, res)` to re-initialize credentials on a 401 response and return updated headers, limiting retries to 2.

*   Update `A2AAuthProviderFactory` in `packages/core/src/agents/auth-provider/factory.ts`:
    *   Implement the `create` method to handle `type: 'http'` by constructing an `HttpAuthProvider`, initializing it, and returning it.

*   Extend `HttpAuthConfig` in `packages/core/src/agents/auth-provider/types.ts`:
    *   Include a variant with `scheme: string` and `value: string` for generic/raw schemes.

*   Modify behavior in `packages/core/src/agents/agentLoader.ts`:
    *   Validate HTTP auth schema to accept any non-empty `scheme` and optional `value`.

*   Update `packages/core/src/agents/registry.ts` for agent registration:
    *   Call `A2AAuthProviderFactory.create` with the agent's `auth` configuration.
    *   If the factory returns `undefined`, do not register the agent and log a warning with `'Error loading A2A agent'`.

*   Adjust `packages/core/src/agents/remote-invocation.ts` for agent execution:
    *   Call `clientManager.loadAgent` with `undefined` as the auth handler if no `auth` is configured.
    *   Handle cases where `A2AAuthProviderFactory.create` returns `undefined` by returning an error message containing `"Failed to create auth provider for agent '<agentName>'"`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.