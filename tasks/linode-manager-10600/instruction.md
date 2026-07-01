Upgrade the OAuth callback flow to enhance security by implementing proper validation and token exchange. Ensure the callback page performs necessary checks and handles failures by redirecting users to logout. Update the authentication store to persist critical fields across sessions.

*   Implement the `OAuthCallbackPage` component in `packages/manager/src/layouts/OAuth.tsx`:
    *   Redirect to `LOGIN_ROOT + '/logout'` using `window.location.assign()` if:
        *   No code verifier exists in `localStorage` under 'authentication/code-verifier'.
        *   The URL query string lacks a 'code' parameter.
        *   The 'state' parameter in the URL does not match the nonce in `localStorage` under 'authentication/nonce'.
    *   Make a POST fetch request to `LOGIN_ROOT + '/oauth/token'` with a `FormData` body if code, code verifier, and nonce checks pass.
    *   Redirect to the logout path using `window.location.assign()` if the token exchange POST request returns a non-ok response.
    *   On successful token exchange, call `dispatchStartSession` with `access_token`, `token_type`, `scopes`, and an expiry date string, then use `history.push()` with the `returnTo` path from URL params.
    *   Ensure `dispatchStartSession` is only called on successful token exchange.

*   Define the `OAuthQueryParams` type in `packages/manager/src/layouts/OAuth.tsx`:
    *   Include string fields: `code`, `returnTo`, and `state`.

*   Update the `authentication` storage object in `src/utilities/storage.ts`:
    *   Add a `codeVerifier` field with `.get()` and `.set(v)` methods, persisting values in `localStorage` under 'authentication/code-verifier'.
    *   Ensure `authentication.nonce` and `authentication.codeVerifier` are not cleared when `handleLogout()` is dispatched.
    *   Clear other authentication fields (expire, scopes, token) to an empty string on `handleLogout()`.

*   Ensure `OAuthCallbackPage` is a named export and follows the signature: `OAuthCallbackPage(props: CombinedProps) -> JSX.Element`.

*   Ensure `CombinedProps` is a named export with the signature:
    *   `{ dispatchStartSession: (token: string, tokenType: string, scopes: string, expiry: string) => void; history: History; location: Location; match: Match }`.

*   Ensure `authentication.codeVerifier` is a named export with the signature:
    *   `{ get: () => string; set: (v: string) => void }`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.