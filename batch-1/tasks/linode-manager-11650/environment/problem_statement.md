## Description

The application currently manages authentication state (session token, expiration, scopes) through a centralized application state store. Components throughout the app have to connect to this store to read whether a user is logged in or is an admin impersonating a customer account, and any change to the token data requires dispatching actions through the store. This tight coupling makes authentication logic harder to test and reason about in isolation.

We need to replace this state-store-based authentication management with a simpler dedicated utility module that reads and writes auth token data directly to local storage. Components that currently rely on store selectors to check login status or admin status should use the new utility functions instead.

## Expected Behavior

- A utility module provides functions to get, set, and clear the session token (along with its expiration and scopes). Clearing must result in all fields being empty strings.
- A function must be available to check whether the currently logged-in user is an administrator impersonating a customer, determined from the token value itself (not from derived store state).
- The logout component must use the new utility to clear session data on mount. No store-connected props should be required.
- The OAuth callback component must use the new utility to save the session token after a successful code exchange. No store-connected props should be required.
- On any OAuth callback failure, the session token must be cleared and the user redirected to the login server's logout endpoint.
- When an expired or unauthorized response is received from the API, the session token must be cleared and the user redirected directly to the login page.
- After token expiration in the parent/child account switching flow, users are redirected to the login page (not the logout page).

## Why This Matters

Removing the centralized store from the authentication flow makes the session token accessible everywhere without requiring a store connection, simplifies component testing (no custom store setup needed), and makes the behavior of session management easier to trace and verify.
