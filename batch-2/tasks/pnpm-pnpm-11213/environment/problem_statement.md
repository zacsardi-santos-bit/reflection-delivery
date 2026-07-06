## Description

There is currently no logout command in pnpm. When users want to stop being authenticated against a registry, they have no clean way to revoke their token on the registry and remove it from local configuration files through pnpm itself — they have to do everything manually.

## Expected Behavior

- Running the logout command against a registry where the user is not logged in should report an error clearly stating which registry has no active session.
- When logged in, the command should contact the registry to revoke the authentication token and then remove it from pnpm's local credential storage automatically.
- If the registry is unreachable (e.g., due to a network error), or if the registry returns an error response, the command should still clean up the local token and inform the user of the issue rather than failing completely.
- If the token was stored in an external configuration file (not pnpm's own credential file), the command should still revoke it on the registry, but warn the user that the credential could not be automatically removed and must be cleaned up manually.
- If the registry call fails and there is no local credential to remove either, the command should fail with a clear error message explaining the situation.
- Custom registry URLs (including those with path components) should be supported in addition to the default registry.

## Why This Matters

Without a dedicated logout command, users cannot easily revoke credentials and clean up their local configuration, which is a security concern. The command should handle the most common edge cases gracefully so that users always end up in a consistent, known state after running it.
