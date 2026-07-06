## Description

The ShellHub authentication system currently has no protection against brute-force login attacks. An attacker can make unlimited failed login attempts against any account without being blocked. We need an account lockout feature that temporarily blocks a source IP address from making further login attempts against a specific account after too many consecutive failures.

## Expected Behavior

- When a user authentication attempt fails due to a wrong password, the system should record the failed attempt, tracking the source IP address and the targeted account.
- After a configurable number of consecutive failed attempts from the same source IP for a given account, that IP should be temporarily blocked from further authentication attempts for that account.
- While a lockout is in effect, the authentication endpoint must return a "Too Many Requests" response (HTTP 429) to communicate that the account is temporarily locked.
- When a user successfully authenticates, any accumulated failed attempt count for that IP/account combination should be cleared.
- The login page should display an informative alert to the user when they encounter a lockout, so they understand why the authentication is being rejected.

## Why This Matters

Without this protection, accounts are vulnerable to automated password-guessing attacks. This feature adds a basic but critical layer of protection while also giving users clear feedback when access is blocked, rather than a generic unauthorized error.
