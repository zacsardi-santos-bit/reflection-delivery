## Description

The secret management system currently only supports service-scoped secrets — every secret must be associated with a specific named service. This makes it impossible to manage secrets that need to be shared across multiple services without duplicating them. We need support for "shared secrets" that are stored in a common location and can be referenced by any service using a distinct format.

## Expected Behavior

- Secret references with a shared prefix should be recognized as valid secret references just like regular service-scoped ones.
- The CLI tool for secret management should accept an option to act on a shared secret rather than a service-specific one. Shared secrets must require a cluster list but do not require a service name.
- When adding or updating a shared secret, the helper output should reflect the correct reference format for shared secrets.
- When running a service locally, the runtime should automatically detect shared secret references in environment variables and decrypt them using the shared service context, while continuing to decrypt service-specific secrets normally. Both sets of decrypted secrets should be combined and returned together.
- Attempting to manage a shared secret without specifying the required cluster list should result in a clear error and early exit.

## Why This Matters

Without shared secrets, teams are forced to duplicate the same secret across multiple services, creating operational overhead and consistency risks. Supporting a shared secret type allows a single secret to be referenced by any service that needs it, simplifying secret management across a platform.
