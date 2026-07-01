Fix the three related bugs in the NATS server's JWT account import handling. Ensure proper logging, prevent deadlocks, and eliminate duplicate account registrations.

*   Implement error logging for activation claims:
    *   Log an error message when an activation claim's IssuerAccount field does not match the exporting account's name.
    *   Format the log message as: "Invalid issuer account %q in activation claim (subject: %q - type: %q) for account %q".
    *   Ensure two error log entries are emitted for two invalid imports with incorrect IssuerAccount values.

*   Prevent deadlocks in import validation:
    *   Ensure the import validation process does not deadlock when using signing keys, even if another goroutine holds the exporter account's write lock.
    *   Avoid acquiring the account's read lock a second time during issuer verification.

*   Eliminate duplicate account registrations:
    *   Ensure the server's `tmpAccounts` map is empty after concurrent account lookups and fetches across a gateway cluster.
    *   Modify `registerAccountNoLock` in `server/server.go` to return a non-nil *Account if the account is already registered, and nil if newly registered.
    *   Ensure callers handle the return value to use the already-registered account if applicable.

*   Ensure message delivery across gateway servers:
    *   When messages are published from one gateway server and a subscription is created on another, ensure messages are delivered once the subscription is registered.

*   Allow sufficient time for gateway outbound connection establishment:
    *   Ensure TLS-authenticated clusters have at least 5 seconds for hostname resolution and connection attempts.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.