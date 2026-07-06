## Description

The Soroban SDK's contract macro system does not properly recognize the built-in duration and timepoint value types when generating machine-readable contract specifications. When a contract function is written to accept a duration or a timepoint value, the generated XDR specification incorrectly classifies those parameter types as unknown user-defined types instead of their proper built-in type definitions.

## Expected Behavior

- When a smart contract function accepts a duration value as a parameter, the generated contract specification should reflect the correct built-in duration type — not fall back to a user-defined/opaque type.
- When a smart contract function accepts a timepoint value as a parameter, the generated contract specification should reflect the correct built-in timepoint type.
- Contracts with duration or timepoint parameters should be executable at runtime, with the values correctly passed through.

## Why This Matters

Tooling that reads the contract's XDR specification (for client generation, documentation, or interoperability) relies on accurate type information. If duration and timepoint types are misclassified, downstream consumers of the spec will not understand the contract's interface correctly, leading to broken integrations and incorrect client-side type handling.
