Upgrade the arithmetic library dependencies in Noir's circuit system to the latest major versions. Ensure compatibility with older versions used by external cryptographic libraries in tests by introducing compatibility aliases and implementing necessary serialization methods.

*   Upgrade Dependencies:
    *   Update the workspace-level `Cargo.toml` to use version `^0.5.0` for the following dependencies:
        *   `ark-bn254`
        *   `ark-bls12-381`
        *   `ark-ec`
        *   `ark-ff`
        *   `ark-std`

*   Add Compatibility Aliases:
    *   In `acvm-repo/acvm/Cargo.toml` under `dev-dependencies`, add:
        *   `ark-bn254-v04 = { package = "ark-bn254", version = "^0.4.0", default-features = false, features = ["curve"] }`
        *   `ark-ff-v04 = { package = "ark-ff", version = "^0.4.0", default-features = false }`
    *   In `tooling/nargo_cli/Cargo.toml` under `dev-dependencies`, add the same aliases with identical specifications.

*   Implement Serialization Methods:
    *   Ensure the `FieldElement` type in the `acir` crate provides:
        *   `fn to_be_bytes(&self) -> Vec<u8>` as a method on the `AcirField` trait, returning a big-endian byte vector.
        *   `fn from_be_bytes_reduce(bytes: &[u8]) -> FieldElement` as an associated function, constructing a `FieldElement` from a big-endian byte slice, reducing modulo the field order.
    *   Ensure these methods support a correct round-trip conversion between byte representations and field elements.

*   Ensure Test Compatibility:
    *   Verify that the `Poseidon2` permutation implementation matches the external `zkhash` reference implementation when bridging field values between versions using big-endian bytes.
    *   Confirm that the `Poseidon` hash implementation matches the external `light_poseidon` reference implementation when inputs and outputs are converted using the `to_be_bytes()` and `from_be_bytes_reduce()` methods.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.