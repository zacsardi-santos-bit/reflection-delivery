Improve the Gleam compiler's error reporting by ensuring it continues analyzing modules even after encountering specific errors. Implement fault-tolerant behavior so that all errors are reported in a single pass, enhancing the developer experience.

*   Implement error reporting for functions:
    *   Emit 'Function without an implementation' error when a function lacks a body and external implementation, but continue analyzing subsequent definitions.
    *   Register functions with body type errors using their annotations, allowing proper type checking for subsequent calls.
    *   Emit type mismatch errors when a function's body type does not match its return type annotation, but continue analysis.
    *   Emit 'Invalid JavaScript function' error for invalid external implementations, but continue analyzing subsequent functions.
    *   Emit 'Missing type annotation' error for external functions lacking required annotations, but continue analysis.
    *   Emit 'Unsupported target' error for functions with only Erlang implementations when compiling for JavaScript, and ensure call sites produce a separate error with a hint about target mismatch.

*   Implement error reporting for type definitions:
    *   Emit 'Duplicate type parameter' error for custom type definitions with duplicate parameters, but continue analyzing subsequent definitions.
    *   Emit 'Duplicate type parameter' error for type aliases with duplicate parameters, but continue analyzing subsequent aliases.

*   Update the analysis phase failure struct:
    *   Rename `InferenceFailure` to `AnalysisFailure` in the `compiler-core/src/analyse/` directory.
    *   Ensure `AnalysisFailure` exposes an `errors` field containing collected errors.

*   Implement the test macro:
    *   Use `assert_js_module_error!(source_code_str)` in `compiler-core/src/lib.rs` to compile Gleam module snippets targeting JavaScript and assert module-level compilation errors.
    *   Ensure the macro is snapshot-based, comparing results against snapshot files.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.