Implement a new integration test suite for Wasmtime's WebAssembly compilation pipeline. This suite should allow developers to place WebAssembly text-format files as test cases in a designated directory, specify test configurations through embedded directives, and verify that actual compilation outputs match expected results.

*   Make the `test_wasm` module in `cranelift/filetests/src/lib.rs` public to allow external access.
*   Implement the `parse_test_config` function:
    *   Make it public and generic over types implementing `DeserializeOwned`.
    *   Extract lines from the start of a WAT string prefixed with `;;!`, strip the prefix, join them, and deserialize as TOML.
    *   Return an error if TOML parsing fails.
*   Define the `TestKind` enum:
    *   Make it public and deserializable from lowercase strings (`clif`, `compile`, `optimize`).
    *   Set `Clif` as the default variant.
*   Implement the `run_functions` function:
    *   Make it public with the signature: `run_functions(path: &Path, wat: &str, isa: &dyn TargetIsa, kind: TestKind, funcs: &[Function]) -> Result<()>`.
    *   Apply transformations based on `TestKind` and compare output against expected `;;`-prefixed comments in the WAT string.
*   Add a new integration test binary `disas` in `Cargo.toml` with `harness = false`.
    *   Include `cranelift-filetests`, `cranelift-codegen`, and `cranelift-reader` as dev-dependencies.
    *   Enable the `all-arch` feature for the `wasmtime` dev-dependency.
*   Develop a test harness in `tests/disas.rs`:
    *   Scan the `tests/disas` directory for WAT files.
    *   Parse each file's configuration using `parse_test_config`.
    *   Use Wasmtime's engine to compile the module with CLIF emission enabled.
    *   Write CLIF files to a temporary directory and parse them using `cranelift_reader::parse_functions`.
    *   Call `run_functions` to compare against expectations.
*   Ensure WAT test files in `tests/disas/` support:
    *   A `target` field (required).
    *   A `test` field accepting `clif`, `compile`, or `optimize` (optional, defaulting to `clif`).
    *   A `flags` field for Wasmtime CLI flags (optional).
*   Update expected outputs for WAT test files moved from `cranelift/filetests/filetests/wasm/` to `tests/disas/` to reflect Wasmtime's calling convention and memory model.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.