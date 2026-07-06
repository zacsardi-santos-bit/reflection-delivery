Implement a new utility module to manage authentication state directly through local storage, replacing the current centralized store-based approach. Update components to use this utility for session token management, ensuring seamless authentication flow and simplifying testing.

*   Create a utility file at `src/utilities/authentication.ts` with the following functions:
    *   `getAuthToken()`: Return an object `{ expiration: string, scopes: string, token: string }` from local storage. Return empty strings for all fields if no token is stored.
    *   `setAuthToken(authToken: AuthToken)`: Persist the `AuthToken` object to local storage.
    *   `clearAuthToken()`: Reset all token fields to empty strings. Ensure `getAuthToken()` returns `{ expiration: '', scopes: '', token: '' }` afterward.
    *   `isLoggedInAsCustomer()`: Return `true` if the token contains 'admin' (case-insensitive), otherwise return `false`.

*   Update components to use the new utility:
    *   Modify `Logout` component in `src/layouts/Logout.tsx`:
        *   Export `Logout` as a named component that accepts no props.
        *   Call `clearAuthToken()` on mount to clear session data.
    *   Modify `OAuthCallback` component in `src/layouts/OAuthCallback.tsx`:
        *   Export `OAuthCallback` as a named component that accepts no props.
        *   Use `useLocation` and `useHistory` hooks to handle OAuth callbacks.
        *   On successful token exchange, call `setAuthToken()` with formatted token data and redirect using `history.push`.
        *   On failure, call `clearAuthToken()` and redirect to the login server's `/logout` path.

*   Implement error handling and redirection:
    *   In `src/request.tsx`, update `handleError` to:
        *   Call `clearAuthToken()` and redirect to a URL with 'login.linode.com' on 401 HTTP errors.
        *   Ensure non-401 errors do not clear the auth token.
    *   Ensure `setupInterceptors` reads the session token using `getAuthToken().token`.

*   Define and export types:
    *   `AuthToken` interface in `src/utilities/authentication.ts` with shape `{ expiration: string; scopes: string; token: string }`.
    *   `OAuthQueryParams` type in `src/layouts/OAuthCallback.tsx` with shape `{ code: string; returnTo?: string; state: string }`.

*   Ensure specific behavior for account switching:
    *   Redirect to `/login` after token expiration in the Parent/Child account switching flow.

*   Maintain correct API response handling:
    *   Ensure `account/users` endpoint returns 200 responses during `UserSSHKeyPanel` rendering.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.