## Description

There are three related bugs in NATS server's JWT account import handling that need to be fixed together.

**1. Silent failure for activation tokens with wrong issuer account**

When an account sets up an import from another account using a signed activation token, and that token has an incorrect "issuer account" field (for example, pointing to the importing account instead of the exporting account), the server silently refuses the import with no diagnostic output. There is no log message to help operators understand why the import was rejected. The server should detect this misconfiguration and log a clear error message that names the wrong issuer account, the import subject, the import type, and the account being imported into.

**2. Deadlock when importing from accounts with signing keys**

When an account uses signing keys to sign activation tokens for its exports, and a client connects with an import that uses such a token, the import authorization process can deadlock. This happens when another goroutine concurrently holds the exporter account's write lock. The validation code acquires locks in a way that creates a cycle.

**3. Race condition causes duplicate account registration**

In a gateway cluster, when a user account is looked up or fetched concurrently (e.g., due to an optimistic message send on one side and a client connect on the other, with a slow account resolver), the same account can be registered twice. This leaves stale entries in the server's internal temporary account tracking state and can cause routing issues.

## Expected Behavior

- When an activation claim has an issuer account field that does not match the exporting account, log an error and reject the authorization. Two imports with wrong issuer accounts should produce two error log entries.
- Clients connecting with imports from signing-key-based accounts must not deadlock.
- After any combination of concurrent account fetches and registrations completes, the server's temporary account tracking map must be empty (no duplicate registrations).

## Why This Matters

These bugs make JWT-secured clusters unreliable: misconfigurations are silent, certain valid setups cause hangs, and gateway clusters under load can have inconsistent account state.
