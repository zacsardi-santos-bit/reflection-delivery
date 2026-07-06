## Description

The OAuth callback flow needs to be upgraded to support a more secure authorization code exchange. Right now, when users are redirected back to the app after logging in, the callback page does minimal validation before granting session access. We need it to perform proper security checks and exchange the authorization code for tokens via an actual server call.

## Expected Behavior

- When a user lands on the OAuth callback URL, the app should verify that a previously generated secret (code verifier) was saved to browser storage before proceeding.
- The app should also confirm that the authorization state in the URL matches the stored verification value (nonce) to prevent request forgery.
- The app should exchange the authorization code with the authorization server, and only start a session if that exchange succeeds.
- If any of these checks fail — missing code verifier, mismatched state, missing code parameter, or failed token exchange — the user should be redirected to logout rather than being left in a broken session state.
- The authentication store should track the code secret as a new piece of state, and both the nonce and this new secret should be preserved when the user logs out (so they remain available for re-authentication).

## Why This Matters

Without these checks, the OAuth callback could be exploited or lead to invalid sessions. Implementing proper authorization code verification and token exchange improves security and aligns the app with standard secure authentication practices.
