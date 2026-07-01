Update the SDK's authentication module to use consistent OAuth 2.0 terminology instead of OIDC. Rename modules, types, methods, and error variants to reflect the OAuth 2.0 protocol accurately. Ensure all public API surfaces are updated to avoid confusion for developers integrating the SDK.

*   Rename module paths:
    *   Change `crates/matrix-sdk/src/authentication/oidc/` to `crates/matrix-sdk/src/authentication/oauth/`.
    *   Ensure all public types are importable from `crate::authentication::oauth::`.

*   Update `Client` struct:
    *   Replace the `oidc()` method with `oauth()` that returns an `OAuth` instance.

*   Rename main authentication struct:
    *   Change `Oidc` to `OAuth`.
    *   Ensure methods include `data()`, `issuer()`, `client_id()`, `url_for_oidc()`, `login()`, `login_with_oidc_callback()`, `finish_authorization()`, `restore_session()`, `user_session()`, `full_session()`, `refresh_access_token()`, `register_client()`, `account_management_url()`, `server_metadata()`.

*   Update authorization data type:
    *   Rename `OidcAuthorizationData` to `OAuthAuthorizationData`.
    *   Include fields `url` and `state`.

*   Revise error types:
    *   Change `OidcError` to `OAuthError` with variants `ClientRegistration(OAuthClientRegistrationError)`, `AuthorizationCode(OAuthAuthorizationCodeError)`, `RefreshToken(_)`.
    *   Implement `is_not_supported()` method for `OAuthError`.
    *   Rename `OauthClientRegistrationError` to `OAuthClientRegistrationError` with `NotSupported` variant.
    *   Rename `OauthAuthorizationCodeError` to `OAuthAuthorizationCodeError` with `Cancelled` and `InvalidState` variants.

*   Adjust top-level error handling:
    *   Modify `Error` enum to include `OAuth(OAuthError)` variant.
    *   Update `RefreshTokenError` enum to include `OAuth` variant wrapping `OAuthError`.

*   Update cross-signing reset auth type:
    *   Change `CrossSigningResetAuthType::Oidc` to `CrossSigningResetAuthType::OAuth` with `approval_url` field.

*   Modify client inner caches:
    *   Rename `provider_metadata` field to `server_metadata`.
    *   Use cache key `"SERVER_METADATA"` instead of `"PROVIDER_METADATA"`.

*   Update server metadata method:
    *   Replace `provider_metadata()` with `server_metadata()` in `OAuth` struct.
    *   Ensure it returns an error with `is_not_supported()` == true when the endpoint is unavailable.

*   Adjust registrations storage path:
    *   Use `"matrix-sdk-oauth"` as a path component instead of `"oidc"`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.