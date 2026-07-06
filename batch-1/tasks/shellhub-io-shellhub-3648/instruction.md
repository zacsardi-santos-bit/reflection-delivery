Implement an account lockout feature to protect against brute-force login attacks by temporarily blocking IP addresses after too many failed login attempts. Update the authentication service to handle lockouts and provide user feedback when lockouts occur.

*   Update the `AuthUser` method in `api/services/auth.go`:
    *   Accept a third parameter, `sourceIP` (string).
    *   Return three values: `*models.UserAuthResponse`, `int64` lockout timestamp, and `error`.
    *   Check lockout status using `HasAccountLockout`. If locked, return `(nil, lockout_timestamp, ErrAuthUnathorized)`.
    *   On password failure without active lockout, call `StoreLoginAttempt` and return its lockout timestamp with `ErrAuthUnathorized`.
    *   On password success, call `ResetLoginAttempts` and proceed with authentication, returning a lockout timestamp of 0.
    *   For non-lockout failures, return a lockout timestamp of 0.

*   Update the `Cache` interface in `pkg/cache/cache.go`:
    *   Add `HasAccountLockout(ctx context.Context, sourceIP, userID string) (int64, int, error)`.
    *   Add `StoreLoginAttempt(ctx context.Context, sourceIP, userID string) (int64, int, error)`.
    *   Add `ResetLoginAttempts(ctx context.Context, sourceIP, userID string) error`.

*   Modify the HTTP route handler for user authentication:
    *   Check the lockout timestamp from `AuthUser`.
    *   Respond with HTTP 429 (Too Many Requests) if the lockout timestamp is greater than 0.

*   Update the `Login` view component in `ui/src/views/Login.vue`:
    *   Include an alert element with `data-test="invalid-login-alert"`.
    *   Ensure the alert is initially hidden but becomes visible when receiving an HTTP 429 response.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.