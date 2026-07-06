Implement a new output-mode type to improve how test output is handled during circuit execution in the Noir toolchain. Replace the existing boolean parameter with a richer type that supports multiple output options. Update relevant functions and all existing call sites to use this new type.

*   Introduce a new `PrintOutput` enum in `tooling/nargo/src/foreign_calls/print.rs` with the following variants:
    *   `None` — suppresses all output (default variant).
    *   `Stdout` — sends output directly to standard output.
    *   `String(&'a mut String)` — appends output to a caller-supplied string buffer.
    *   Ensure `PrintOutput` has a lifetime parameter `'a`, and derives `Debug` and `Default`.
    *   Re-export `PrintOutput` from `tooling/nargo/src/lib.rs` as `nargo::PrintOutput`.

*   Update `DefaultForeignCallExecutor` in `tooling/nargo/src/foreign_calls/mod.rs`:
    *   Add a lifetime parameter `'a` to hold a `PrintOutput<'a>`.
    *   Modify the `new` constructor signature to accept `output: PrintOutput<'a>` instead of `show_output: bool`.
    *   Ensure passing `PrintOutput::None` suppresses output, `PrintOutput::Stdout` sends output to standard output, and `PrintOutput::String` appends to the provided buffer.

*   Modify the `run_test` function in `tooling/nargo/src/ops/test.rs`:
    *   Change the `show_output: bool` parameter to `output: PrintOutput<'_>`.
    *   Ensure `PrintOutput::None` suppresses output, and `PrintOutput::Stdout` sends output to standard output during test execution.

*   Update all existing call sites:
    *   Replace boolean parameters with the appropriate `PrintOutput` variant.
    *   Map `true` to `PrintOutput::Stdout` and `false` to `PrintOutput::None` to preserve original behavior.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.