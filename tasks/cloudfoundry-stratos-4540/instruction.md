Implement configurable access control for API keys in the Stratos API. Introduce a configuration setting with three modes: disabled, admin users only, and all users. Modify API key-related handlers and middleware to respect this configuration, ensuring proper access control and error handling.

*   Update the `addAPIKey` method in `src/jetstream/apikeys.go`:
    *   Check `Config.APIKeysEnabled` before processing.
    *   Return HTTP 403 with "API keys are disabled" if mode is 'disabled'.
    *   If 'admin_only', call `StratosAuthService.GetUser` and handle errors or non-admin users with HTTP 403.
    *   If 'all_users', maintain existing behavior.

*   Update the `listAPIKeys` method in `src/jetstream/apikeys.go`:
    *   Check `Config.APIKeysEnabled` before processing.
    *   Return HTTP 403 with "API keys are disabled" if mode is 'disabled'.
    *   Otherwise, call `APIKeysRepository.ListAPIKeys` and return results as JSON.

*   Update the `deleteAPIKey` method in `src/jetstream/apikeys.go`:
    *   Check `Config.APIKeysEnabled` before processing.
    *   Return HTTP 403 with "API keys are disabled" if mode is 'disabled'.
    *   Validate `guid` parameter and call `APIKeysRepository.DeleteAPIKey`.

*   Modify the `apiKeyMiddleware` method in `src/jetstream/middleware.go`:
    *   Accept an `echo.HandlerFunc` and return an `echo.HandlerFunc`.
    *   If mode is 'disabled', pass through without setting context values.
    *   For 'all_users', authenticate valid API keys, set `user_id` and `APIKeySkipperContextKey`, and call `UpdateAPIKeyLastUsed`.
    *   For 'admin_only', ensure only admin users are authenticated.

*   Define `APIKeySkipperContextKey` in `src/jetstream/middleware.go`:
    *   Use as the context key for marking requests authenticated via API key.

*   Define `APIKeysConfigEnum` and `APIKeysConfigValue` in `src/jetstream/repository/interfaces/config/config.go`:
    *   `APIKeysConfigEnum` should include `Disabled`, `AdminOnly`, and `AllUsers`.
    *   `APIKeysConfigValue` should be a string-based type.

*   Update the `PortalConfig` struct in `src/jetstream/repository/interfaces/structs.go`:
    *   Add `APIKeysEnabled` field of type `config.APIKeysConfigValue`.
    *   Default to `config.APIKeysConfigEnum.AdminOnly` if unset.

*   Modify the session verification endpoint (`verifySession / getInfo`):
    *   Include `APIKeysEnabled` in the response configuration object.

*   Create a mock for `StratosAuth` interface in `src/jetstream/repository/mock_interfaces/mock_auth.go`:
    *   Use MockGen to create `NewMockStratosAuth(ctrl)` and implement `GetUser`.

*   Ensure `APIKeysRepository` interface includes:
    *   `GetAPIKeyBySecret(secret string) (*interfaces.APIKey, error)`
    *   `UpdateAPIKeyLastUsed(keyGUID string) error`

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.