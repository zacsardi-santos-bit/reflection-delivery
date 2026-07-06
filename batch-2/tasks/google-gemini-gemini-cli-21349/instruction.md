Add support for a gateway-based authentication method to the CLI. Update the authentication logic to include a new method that allows users to authenticate through an external gateway using a base URL and custom headers. Ensure proper error handling and integration with existing authentication flows.

*   Update `refreshAuth` method in `packages/core/src/config/config.ts`:
    *   Accept two new optional parameters after `apiKey`: `baseUrl` (string or undefined) and `headers` (object or undefined).
    *   Full signature: `refreshAuth(authMethod: AuthType, apiKey?: string, baseUrl?: string, customHeaders?: Record<string, string>): Promise<void>`.
    *   Ensure existing callers pass `undefined` for these new parameters.

*   Modify the `GeminiAgent` class in `packages/cli/src/acp/acpClient.ts`:
    *   Extend `authenticate` method to handle `AuthType.GATEWAY`:
        *   Extract `baseUrl` and `headers` from `request._meta.gateway`.
        *   Call `refreshAuth` with `(AuthType.GATEWAY, undefined, baseUrl, headers)`.
        *   Throw an error with message matching `/Malformed gateway payload/` if `baseUrl` is not a string.
    *   Save the setting `security.auth.selectedType` to `AuthType.GATEWAY` using `SettingScope.User`.

*   Update authentication methods list in `GeminiAgent`:
    *   Include a new `gateway` entry in the list of advertised authentication methods.
    *   Ensure the total count of methods is 4.
    *   The `gateway` entry must have `id` equal to `AuthType.GATEWAY` and a `_meta` field of `{ gateway: { protocol: 'google', restartRequired: 'false' } }`.

*   Define `AuthType.GATEWAY` in `packages/core/src/core/contentGenerator.ts`:
    *   Add a new enum value with string value `'gateway'`.
    *   Use this value as `methodId` in authenticate requests and as `id` in the advertised `authMethods` list.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.