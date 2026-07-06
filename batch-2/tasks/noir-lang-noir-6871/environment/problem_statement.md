## Description

The arithmetic library dependencies used throughout Noir's circuit system need to be upgraded to newer versions. However, several external cryptographic libraries used in tests (for reference implementations of Poseidon and Poseidon2 hashing) depend on older versions of these same arithmetic libraries. Because the old and new versions define incompatible types for field elements, tests that compare Noir's hash output against these reference implementations fail to compile.

## Expected Behavior

- The core arithmetic library dependencies should be upgraded to their latest major versions across the workspace.
- For test code that interoperates with external cryptographic reference libraries, compatibility aliases should be introduced that make both the old and new versions of the arithmetic libraries available simultaneously under distinct names.
- The field element type should support serialization to and deserialization from raw big-endian byte arrays, providing a version-agnostic bridge between the old and new type systems.
- Poseidon and Poseidon2 hash equivalence tests should compile and produce results matching their respective external reference implementations.
- All existing solver tests should continue to compile and pass without modification.

## Why This Matters

Keeping arithmetic library dependencies up to date is important for security and performance. Without a clear strategy for bridging between old and new versions during the transition period, a single dependency upgrade can cascade into widespread test failures across the project.
