## Description

The git credential management code in the auth package has grown organically and now mixes several distinct concerns in a single place: detecting which credential helper is configured, registering the CLI tool as a credential provider for a hostname, and updating stored credentials. This makes the code harder to test in isolation and harder to extend.

## Proposed Change

Extract the credential helper configuration and credential update logic into a dedicated sub-package within the auth shared package. This sub-package should provide:

- A type that represents a configured credential helper and can report whether it belongs to the CLI tool or to a third-party helper
- A type that manages reading and writing git credential helper configuration in global git config for a given hostname (including the corresponding gist host)
- A type that stores and replaces git credentials using the underlying git credential system

The rest of the auth package should be updated to depend on these new types rather than on the old mixed implementation. Method names on the existing credential flow type should be updated to reflect the cleaner public API.

## Expected Behavior

- The new sub-package types must be independently testable with an isolated git configuration
- Registering the CLI as a credential helper must work correctly both when no other helpers exist and when pre-existing helpers are already configured
- Querying the configured helper for a hostname must correctly distinguish between no helper, a third-party helper, and the CLI's own helper
- Updating credentials must atomically eject old credentials and store new ones so that subsequent credential lookups return the updated values

## Why This Matters

Cleaner separation of concerns makes individual components easier to test, reason about, and extend. The current mixed implementation makes it difficult to write focused unit tests or to swap out individual pieces of the credential management logic.
