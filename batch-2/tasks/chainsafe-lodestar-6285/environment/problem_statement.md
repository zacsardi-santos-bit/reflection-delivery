## Description

Several third-party libraries that the beacon node depends on have been reorganized, breaking compilation of the CLI package. Ethereum Node Record (ENR) functionality that was previously bundled inside the peer discovery library has been extracted into its own dedicated package. The function used to derive a private key from a peer identity has been renamed in the new package and its return type has changed — the private key is now a field on the returned object rather than the direct return value.

Additionally, networking interface types that were previously imported from multiple distinct sub-paths within the peer-to-peer library are now consolidated under the library's root entry point. Because the source code still references the old package layout and old function signatures, the entire CLI package fails to compile and all related tests fail.

## Expected Behavior

- ENR-related types and utilities should be imported from the new dedicated ENR package rather than from the peer discovery library
- The function for deriving private key material from a peer ID should use the new renamed function and access the private key field on its return value
- Peer identity types should be imported from the root package path rather than from sub-paths
- The beacon node's peer identity initialization logic should correctly create, persist, reload, and validate peer IDs and ENRs under various startup scenarios

## Why This Matters

Without these updates, the codebase fails to compile entirely, meaning no beacon node tests can run and the node cannot be started. Fixing the import paths and updated API call sites restores compilation and all peer identity/ENR initialization behavior.
