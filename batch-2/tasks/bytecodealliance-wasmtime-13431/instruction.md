I've found a security issue in the WASI filesystem implementation.

*   A new WASI Preview 1 test program must be created at crates/test-programs/src/bin/p1_file_truncation_readonly.rs that opens a preopened directory named 'readonly', attempts to open 'test.txt' with the truncation open flag (OFLAGS_TRUNC) and read rights (RIGHTS_FD_READ), and expects the operation to fail with ERRNO_PERM. The program must verify the file contents equal b"truncation test file\n" both before and after the failed truncation attempt.

*   A new WASI Preview 2 component test program must be created at crates/test-programs/src/bin/p2_file_truncation_readonly.rs that locates the preopened directory named 'readonly', attempts to open_at 'test.txt' with OpenFlags::TRUNCATE and DescriptorFlags::READ, and expects the error ErrorCode::NotPermitted. The program must verify the file contents equal b"truncation test file\n" both before and after the failed truncation attempt.

*   The test artifact constants P1_FILE_TRUNCATION_READONLY, P1_FILE_TRUNCATION_READONLY_COMPONENT, and P2_FILE_TRUNCATION_READONLY_COMPONENT must be registered in the test_programs_artifacts crate and available via wildcard import. P1_FILE_TRUNCATION_READONLY must be registered with the foreach_p1! macro; P1_FILE_TRUNCATION_READONLY_COMPONENT and P2_FILE_TRUNCATION_READONLY_COMPONENT must be registered with the foreach_p2! macro.

*   In crates/wasi/src/filesystem.rs, the TRUNCATE open flag handling must be fixed to also require write permission. Specifically, within the block that handles OpenFlags::TRUNCATE, open_mode must be OR-assigned with OpenMode::WRITE so that the permission check rejects guests with only FilePerms::READ.

*   When the wasmtime-wasi runtime processes a file open request with the truncation flag set on a file in a directory preopened with only FilePerms::READ (not write), the operation must fail with a 'not permitted' error and the file contents must remain byte-for-byte identical to their pre-run state.

*   The integration test for p1_file_truncation_readonly in crates/wasi/tests/all/p1.rs must configure the WASI context by preopening a temporary directory as 'readonly' with DirPerms::READ | DirPerms::MUTATE and FilePerms::READ, run the guest with a pre-existing file named 'test.txt' containing b"truncation test file\n", and assert that the file contents remain equal to b"truncation test file\n" after the guest exits successfully.

*   The run helper function in crates/wasi/tests/all/p1.rs, crates/wasi/tests/all/p2/async_.rs, and crates/wasi/tests/all/p2/sync.rs must be refactored to accept a builder closure (impl FnOnce(&mut WasiCtxBuilder) for async variants, impl Fn(&mut WasiCtxBuilder) for sync) instead of a bool for stdio inheritance. All existing callers must be updated accordingly.

*   Placeholder test functions named p1_file_truncation_readonly must be added to crates/wasi-common/tests/all/async_.rs and crates/wasi-common/tests/all/sync.rs. These are no-op tests (printing a message only) to satisfy the assert_test_exists constraint from the foreach_p1! macro.


*   Interface details: ## New Test Program Sources

Two new WebAssembly guest program sources must be created:

Type: Rust Source File (compiled to WASM)
Location: crates/test-programs/src/bin/p1_file_truncation_readonly.rs
Description: WASI Preview 1 test program that looks for a preopened directory named "readonly", verifies "test.txt" has expected contents (b"truncation test file\n"), attempts to open that file with OFLAGS_TRUNC and RIGHTS_FD_READ, expects the open to fail with ERRNO_PERM, then verifies the file contents remain unchanged. Uses `open_scratch_directory("readonly")` to get the directory fd.

Type: Rust Source File (compiled to WASM)
Location: crates/test-programs/src/bin/p2_file_truncation_readonly.rs
Description: WASI Preview 2 component test program that finds a preopened directory named "readonly" from `preopens::get_directories()`, verifies "test.txt" has expected contents (b"truncation test file\n"), attempts to open_at that file with OpenFlags::TRUNCATE and DescriptorFlags::READ, expects the result to be Err(ErrorCode::NotPermitted), then verifies the file contents remain unchanged.

## Test Artifact Constants

The following constants must be available from the `test_programs_artifacts` crate (via `use test_programs_artifacts::*`). They follow the naming convention used by other test artifacts — uppercase of the source filename — and are generated from the test program binaries above:

- `P1_FILE_TRUNCATION_READONLY` — path to the compiled P1 WASI binary from `p1_file_truncation_readonly.rs`
- `P1_FILE_TRUNCATION_READONLY_COMPONENT` — path to the compiled P1 WASM component from `p1_file_truncation_readonly.rs`
- `P2_FILE_TRUNCATION_READONLY_COMPONENT` — path to the compiled P2 WASM component from `p2_file_truncation_readonly.rs`

These constants must also be registered with the `foreach_p1!` macro (for `P1_FILE_TRUNCATION_READONLY`) and `foreach_p2!` macro (for `P1_FILE_TRUNCATION_READONLY_COMPONENT` and `P2_FILE_TRUNCATION_READONLY_COMPONENT`) so that `assert_test_exists` is satisfied.

## Bug Fix Location

Type: Implementation Fix
Location: crates/wasi/src/filesystem.rs
Description: In the code block that handles `OpenFlags::TRUNCATE`, `open_mode` must be OR-assigned with `OpenMode::WRITE` so that the existing permission check (`open_mode` vs `FilePerms`) correctly rejects guests that only have `FilePerms::READ`. Without this, the truncation proceeds even when the guest lacks write permission. The fix should be placed alongside the existing `opts.truncate(true).write(true)` call within the `if oflags.contains(OpenFlags::TRUNCATE)` block.

## Test Harness Signature Change

The `run` helper function in the following test files must be updated to accept a builder closure instead of a `bool`:

- `crates/wasi/tests/all/p1.rs`: `async fn run(path: &str, with_builder: impl FnOnce(&mut WasiCtxBuilder)) -> Result<()>`
- `crates/wasi/tests/all/p2/async_.rs`: `async fn run(path: &str, with_builder: impl FnOnce(&mut WasiCtxBuilder)) -> Result<()>`
- `crates/wasi/tests/all/p2/sync.rs`: `fn run(path: &str, with_builder: impl Fn(&mut WasiCtxBuilder)) -> Result<()>`

`WasiCtxBuilder` is imported from `wasmtime_wasi`.

## Placeholder Tests (wasi-common)

In `crates/wasi-common/tests/all/async_.rs` and `crates/wasi-common/tests/all/sync.rs`, a placeholder test function named `p1_file_truncation_readonly` must be added. This function does not exercise WASI functionality — it only prints a message explaining the test is handled by wasmtime-wasi, to satisfy the `assert_test_exists` requirement from the `foreach_p1!` macro.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.