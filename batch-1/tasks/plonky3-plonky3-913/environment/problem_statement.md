## Description

We have a BN254 scalar field crate in this repository that currently wraps an external elliptic curve library to perform field arithmetic. This brings in a large, unnecessary dependency for what is ultimately a straightforward finite-field implementation. We'd like to replace it with a self-contained implementation that computes field operations natively, without pulling in external cryptographic library code.

At the same time, the crate name and the primary type name carry a trailing qualifier that reflects the "scalar field" of the curve. Other field crates in this project don't use that convention — they just use the curve name. We'd like to rename accordingly: the crate directory, the package name, the workspace member reference, and the exported field type should all drop the scalar-field suffix.

## Expected Behavior

- A renamed crate replaces the old one in the workspace
- The exported field element type is renamed to drop the scalar-field suffix
- The field type correctly implements all standard prime field operations: arithmetic, inversion, integer conversion (both signed and unsigned small values), serialization, and two-adic extension
- The multiplicative generator and two-adicity of the field must be correct
- Serialization and deserialization round-trips are consistent and lossless
- The Poseidon2 hash continues to work correctly with the renamed field type

## Why This Matters

Removing the external cryptographic library dependency makes this crate lighter, compatible with no-std builds, and easier to audit. Renaming to match the project's naming conventions reduces confusion for users familiar with how other field crates in this project are named.
