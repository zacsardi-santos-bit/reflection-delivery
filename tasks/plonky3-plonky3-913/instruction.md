Refactor the BN254 scalar field crate to eliminate its dependency on an external elliptic curve library and implement a self-contained native solution. Rename the crate and exported field type to align with the project's naming conventions. Ensure the new implementation passes all existing tests and maintains compatibility with the Poseidon2 hash permutation.

*   Update the workspace and crate configuration:
    *   In the root `Cargo.toml`, rename the workspace member from `"bn254-fr"` to `"bn254"` and update the dependency to `p3-bn254 = { path = "bn254", version = "0.3.0" }`.
    *   In `poseidon2/Cargo.toml`, change the dev-dependency from `p3-bn254-fr` to `p3-bn254`.
    *   Rename the crate directory from `bn254-fr/` to `bn254/` and update `Cargo.toml` to declare `name = "p3-bn254"`, removing the `halo2curves` dependency.

*   Implement the `Bn254` struct in `bn254/src/bn254.rs`:
    *   Ensure `Bn254` implements traits: `Copy`, `Clone`, `Default`, `Eq`, `PartialEq`, `Hash`, `Ord`, `PartialOrd`, `Display`, `Debug`, `Serialize`, `Deserialize`, `Packable`, `PrimeCharacteristicRing`, `Field`, `PrimeField`, `TwoAdicField`, `RawDataSerializable`, `InjectiveMonomial<5>`, `QuotientMap<u128>`, `QuotientMap<i128>`, `Distribution<Bn254> for StandardUniform`, `Add`, `Sub`, `Mul`, `Div`, `Neg`, and their assign variants.
    *   Define constants: `ZERO`, `ONE`, `TWO`, `NEG_ONE` (via `PrimeCharacteristicRing`), `GENERATOR` (equals field element 5), and `TWO_ADICITY = 28`.
    *   Implement arithmetic operations: addition, subtraction, multiplication, division, negation, and inversion (with `try_inverse`).
    *   Implement conversion from small integers (both signed and unsigned) and ensure `as_canonical_biguint()` recovers the original integer value.
    *   Ensure serialization and deserialization via serde are consistent and lossless.

*   Implement the `Poseidon2Bn254` type alias in `bn254/src/poseidon2.rs`:
    *   Define `Poseidon2Bn254<const WIDTH: usize>` as `Poseidon2<Bn254, Poseidon2ExternalLayerBn254<WIDTH>, Poseidon2InternalLayerBn254, WIDTH>`.
    *   Validate that `Poseidon2Bn254<3>` matches reference outputs with 8 full rounds and 56 partial rounds.

*   Update test modules:
    *   In `bn254/src/bn254.rs`, within `#[cfg(test)] mod tests`:
        *   Create a `#[test]` function `test_bn254fr` to verify canonical representations and serde round-trips.
        *   Invoke `test_field!(crate::Bn254, &[ZERO_CONST], &[ONE_CONST], &prime_factorization_fn())` to generate field tests.
        *   Invoke `test_prime_field!(crate::Bn254)` for prime field tests.
    *   In `bn254/src/poseidon2.rs`, within `#[cfg(test)] mod tests`:
        *   Create a `#[test]` function `test_poseidon2_bn254` to verify permutation outputs against reference constants.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.