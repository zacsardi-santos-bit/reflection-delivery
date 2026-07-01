Implement a procedural macro attribute `#[wasmtime_test]` to automatically generate test variants for each supported WebAssembly compiler backend. Extend this macro to accept a `wasm_features(...)` argument for enabling specific WebAssembly features and adjust test generation accordingly. Remove redundant backend-specific tests from a standalone file.

Requirements:

*   Update the `#[wasmtime_test]` attribute macro in `crates/test-macros/src/lib.rs`:
    *   Accept an optional `wasm_features(...)` argument with feature identifiers: `reference_types`, `function_references`, `gc`, `simd`, `relaxed_simd`, `tail_call`, `threads`.
    *   Allow multiple features to be combined, e.g., `wasm_features(function_references, gc)`.
    *   Suppress Winch strategy test variant generation when `wasm_features(...)` is specified.
        *   Only generate `cranelift_*` variants when any `wasm_features(...)` is present.
    *   Explicitly enable features `gc` and `function_references` in the test harness by calling `config.wasm_gc(true)` and/or `config.wasm_function_references(true)` when these features are specified.
    *   Do not call config methods for features that are on by default: `reference_types`, `simd`, `relaxed_simd`, `tail_call`, `threads`.
*   Ensure `#[wasmtime_test]` without arguments generates both `cranelift_*` and `winch_*` variants, with `winch_*` gated to `target_arch = "x86_64"`.
*   Modify test functions to use `#[wasmtime_test]`:
    *   Functions must accept a `config: &mut Config` parameter.
    *   The macro harness must create `Config::new()`, set the strategy with `config.strategy(Strategy::<Name>)`, apply feature setup for off-by-default features, and call the test function.
    *   Inside the function, create the engine with `Engine::new(&config)` and the store with `Store::new(&engine, ())`.
*   Remove specific test functions and helpers from `tests/all/winch.rs`:
    *   Remove test functions: `array_to_wasm`, `native_to_wasm`, `wasm_to_native`, `mixed_roundtrip`, `native_to_wasm_trap`, `wasm_to_native_trap`.
    *   Remove module-level string constants: `MODULE`, `MIXED`.
    *   Remove helper functions: `add_fn`, `id_float`.
    *   Retain `dynamic_heap` and `static_oob` tests.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.