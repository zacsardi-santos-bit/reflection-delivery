## Description

The setuid endpoint, which handles cookie syncing for user identification across ad exchanges, currently has no account-level validation. When a publisher account is disabled by the host operator, the setuid endpoint still processes requests for that account as if it were active. This means disabled publishers can continue to have user syncing performed on their behalf even after their accounts have been deactivated.

## Expected Behavior

- When a client sends a setuid request that includes an account identifier for a valid, active account, the request should be processed normally and return a successful response.
- When a client sends a setuid request that includes an account identifier for a disabled account, the endpoint should reject the request with a 400 Bad Request response and a message indicating that the account is disabled and the publisher should contact the host.
- The setuid endpoint should accept the full server configuration rather than only the host cookie portion, so it can perform account lookups using the same account service used by other endpoints.

## Why This Matters

Host operators need the ability to disable publisher accounts and have that setting enforced uniformly across all endpoints, including the cookie sync flow. Without this validation, a disabled account can still influence user tracking behavior on the server, which undermines the host's ability to manage publisher relationships. Aligning the setuid endpoint with the account validation already present in other endpoints ensures consistent enforcement of account status throughout the system.
