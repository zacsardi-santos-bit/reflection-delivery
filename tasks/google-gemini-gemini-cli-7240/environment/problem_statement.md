## Description

When the CLI authenticates with external tool servers using OAuth, the acquired tokens are not persisted to disk. This means that every time the CLI restarts, users must go through the OAuth flow again for each connected server — even when their tokens were still valid. There is no file-backed storage that encrypts and saves these credentials between sessions.

## Expected Behavior

- A file-based token storage implementation should be provided that saves OAuth credentials to an encrypted file in the user's home configuration directory.
- The encryption should be non-trivial: the stored file should be unreadable without the correct machine- and user-specific key, and reading a corrupted file should result in a clear error rather than silent failure.
- The storage should support reading credentials for a specific server (returning nothing if the token is expired), saving or updating credentials while preserving entries for other servers, deleting an individual server's credentials (and cleaning up the file when no entries remain), listing all server names, and wiping all stored credentials.
- Errors should be meaningful: attempting to read from or delete credentials in a non-existent file should report that the file does not exist, and attempting to decrypt a file that has been corrupted should report that the file is corrupted.

## Why This Matters

Without persistent token storage, users are forced to re-authenticate on every CLI restart, making workflows that involve multiple OAuth-protected servers significantly more cumbersome. Persisting encrypted tokens to disk allows the CLI to reuse valid sessions transparently, improving the day-to-day experience while keeping credentials secure at rest.
