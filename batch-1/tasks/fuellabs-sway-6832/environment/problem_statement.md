## Description

The Sway standard library is missing built-in types and operations for zero-knowledge cryptography. Specifically, there is no standard way to represent a two-dimensional elliptic curve point or a cryptographic scalar value, and no standard library functions to perform the elliptic curve operations that the underlying VM supports.

## Expected Behavior

- A two-dimensional curve point type should be available in the standard library's crypto module, supporting:
  - Construction from a pair of 256-bit integer values (either sign convention), from arrays of 256-bit integers, and from raw byte arrays
  - Retrieval of the x and y coordinates as byte sequences
  - A zero/minimum value constructor and a check for whether the point equals zero
  - Equality comparison between two points
  - Conversion back to the original numeric formats (tuples and arrays of 256-bit integers)
- A scalar value type should be available in the same module, supporting:
  - Construction from a 256-bit integer or a raw byte array
  - Retrieval of the underlying bytes
  - A zero/minimum value constructor and a zero-check
  - Conversion back to 256-bit integer types
- Three elliptic curve operations should be available (point addition, scalar multiplication, and pairing check) that work with these types and revert on invalid inputs

## Why This Matters

Developers building zero-knowledge proof applications in Sway need to work with elliptic curve mathematics. Without standard types and operations, every team must roll their own representations and handle the underlying VM precompile calls manually. Adding these primitives to the standard library makes ZK development in Sway practical and consistent.
