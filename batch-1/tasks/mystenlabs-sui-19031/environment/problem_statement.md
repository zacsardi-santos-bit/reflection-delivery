## Description

We need to bump the maximum supported protocol version from 54 to 55 in the Sui codebase. Version 55 is intended to enable enum types for smart contract developers on mainnet. Previously, enum support was gated and unavailable on the main network; this protocol version change makes it generally available.

## Expected Behavior

- The protocol configuration recognizes 55 as the maximum supported version, enabling new networks to initialize with protocol version 55 by default.
- Protocol version 55 enables the bytecode format that supports enum types, so smart contracts using enums can be deployed and executed.
- The public API spec (OpenRPC) reflects 55 as the maximum supported protocol version.
- Genesis configuration snapshots and related test fixtures all reflect the updated protocol version and the deterministically-derived on-chain state.

## Why This Matters

Without this upgrade, smart contract developers cannot use enum types on mainnet even though the feature has been validated on testnets. This is a routine protocol version registration step — incrementing the version ceiling, attaching the correct feature flag configuration, and updating all downstream configuration snapshots that record the current maximum version.
