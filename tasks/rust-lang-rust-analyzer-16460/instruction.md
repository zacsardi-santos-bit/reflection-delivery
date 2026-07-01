Implement a new diagnostic in rust-analyzer to detect and fix redundant trailing return expressions in Rust code. Ensure the diagnostic is a weak hint and provides an automated fix to convert explicit returns into idiomatic tail expressions. Cover various contexts where trailing returns can appear, such as function bodies, if/else branches, match arms, closure bodies, and nested inner functions.

*   Add a public function `check_fix_with_disabled` in `crates/ide-diagnostics/src/tests.rs`:
    *   Accepts parameters: `ra_fixture_before: &str`, `ra_fixture_after: &str`, and `disabled: impl Iterator<Item = String>`.
    *   Builds a `DiagnosticsConfig` using `DiagnosticsConfig::test_sample()`.
    *   Sets `expr_fill_default` to `ExprFillDefaultMode::Default`.
    *   Extends the `disabled` set with each `String` from the provided iterator.
    *   Invokes `check_nth_fix_with_config` with `nth=0` and the two fixture strings.

*   Extract a private helper function `check_nth_fix_with_config` in `crates/ide-diagnostics/src/tests.rs`:
    *   Accepts parameters: `config: DiagnosticsConfig`, `nth: usize`, `ra_fixture_before: &str`, and `ra_fixture_after: &str`.
    *   Refactor `check_nth_fix` to delegate to this function using the default configuration.

*   Create a new diagnostic handler `remove_trailing_return` in `crates/ide-diagnostics/src/handlers/remove_trailing_return.rs`:
    *   Register this handler in `crates/ide-diagnostics/src/lib.rs` so `AnyDiagnostic::RemoveTrailingReturn` dispatches to it.
    *   Emit a diagnostic with code `DiagnosticCode::Clippy("needless_return")` and message "replace return <expr>; with <expr>" at weak severity.
    *   Trigger the diagnostic when a return expression is the last statement of a function body, including nested contexts like if/else branches and match arms.
    *   Ensure the diagnostic fires for trailing returns in closure bodies and inner function definitions.
    *   Do not trigger the diagnostic when a return expression is followed by other statements.

*   Attach a fix to the diagnostic:
    *   Assign fix ID "remove_trailing_return" and label "Replace return <expr>; with <expr>".
    *   Replace the entire return statement with the expression as a tail expression when the return has an expression.
    *   Delete the return statement entirely when it has no expression.

*   Write tests for the `remove_trailing_return` diagnostic:
    *   Use `check_fix_with_disabled` or `check_diagnostics_with_disabled` for tests involving if/else branches.
    *   Pass "remove-unnecessary-else" as the disabled diagnostic name to avoid interference.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.