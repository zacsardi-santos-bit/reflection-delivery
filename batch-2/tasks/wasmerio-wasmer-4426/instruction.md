Update the WebAssembly interface library to use the latest version of the wasm parsing dependency. Modify the validation code to work with the new API, ensuring all existing functionality and tests remain operational.

*   Update the `wasmparser` dependency in `lib/wasm-interface/Cargo.toml`:
    *   Change from the old pinned version to the workspace-defined version.
*   Modify the validation code in `lib/wasm-interface/src/validate.rs`:
    *   Replace the deprecated `ValidatingParser/WasmDecoder` approach with `Validator::new_with_features`.
    *   Use `WasmFeatures` struct to configure the validator.
        *   Enable features: `threads`, `reference_types`, `simd`, `bulk_memory`, and `multi_value` by setting them to `true`.
        *   Use `..Default::default()` for all other fields.
    *   Ensure `validate_all` method on the validator can validate a minimal WebAssembly module without errors.
*   Ensure the `validate_wasm_and_report_errors` function:
    *   Continues to validate global imports correctly against an interface, returning errors for mismatched types.
    *   Continues to validate global exports correctly against an interface, returning errors for mismatched types.
*   Verify that all pre-existing parser and interface tests in the `wasmer-wasm-interface` package compile and pass after the dependency update and code changes.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.