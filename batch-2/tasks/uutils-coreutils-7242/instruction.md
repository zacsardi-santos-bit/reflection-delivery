Update the codebase to accommodate the breaking changes introduced in the new major version of the random number generation library. Ensure that the code compiles successfully and all tests pass by making the necessary adjustments to function names, module paths, and method signatures.

*   Update the dependency versions:
    *   Change the `rand` version in `Cargo.toml` and `fuzz/Cargo.toml` from `0.8.x` to `0.9.x`.
    *   Update `rand_core` to a compatible `0.9.x` version.

*   Modify import paths:
    *   Change all imports from `rand::distributions` to `rand::distr`.
    *   Update imports of `rand::seq::SliceRandom` to `rand::prelude::IndexedRandom`.

*   Update function calls:
    *   Replace `rand::thread_rng()` with `rand::rng()` in all source files, test helpers, and fuzz targets.
    *   Change `rng.gen_range(...)` to `rng.random_range(...)`.
    *   Replace `rng.gen_bool(...)` with `rng.random_bool(...)`.
    *   Change `rng.gen_ratio(...)` to `rng.random_ratio(...)`.

*   Adjust method usage:
    *   Update `Uniform::new(low, high)` to `Uniform::new(low, high).unwrap()` to handle the new `Result` return type.
    *   Replace `StdRng::from_entropy()` with `StdRng::from_os_rng()` in the shred source file.
    *   Change `rand::distributions::Standard` to `rand::distr::StandardUniform` in the sort source file.

*   Update method signatures:
    *   Modify the `try_fill_bytes` method in the `ReadRng` adapter to use the new error types from `rand_core` 0.9.x, ensuring it returns a local error type instead of `rand_core::Error`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.