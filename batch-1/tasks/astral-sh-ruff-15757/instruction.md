Update the FURB122 lint rule to correctly identify and handle for-loop writes that should be replaced with a bulk-write operation, while ensuring that the transformation does not alter the program's behavior in specific cases. Implement the necessary changes in the rule logic and update the snapshot file to reflect the accurate diagnostic output.

*   Modify the FURB122 lint rule in `crates/ruff_linter/src/rules/refurb/rules/for_loop_writes.rs`:
    *   Detect for-loop writes with complex loop targets such as empty tuple patterns, tuple unpacking with multiple variables, and nested list/tuple destructuring patterns.
    *   Ensure the rule suggests a bulk-write operation only when safe:
        *   Do not flag loops where the loop variable is declared global or nonlocal.
        *   Do not flag loops where a variable with the same name as the loop target exists before the loop starts.
        *   Do not flag loops where any variable bound by the loop target is referenced after the loop.
    *   When suggesting a fix, preserve the original loop target pattern inside a generator expression.

*   Update the snapshot file at `crates/ruff_linter/src/rules/refurb/snapshots/ruff_linter__rules__refurb__tests__FURB122_FURB122.py.snap`:
    *   Ensure it reflects the correct diagnostic output, including updated line numbers and newly added detections for complex-pattern error cases.
    *   Remove diagnostics for cases involving global/nonlocal variables, prior assignments, and post-loop references.

*   If necessary, adjust integration points:
    *   Review `crates/ruff_linter/src/checkers/ast/analyze/statement.rs` for any needed changes in the statement analysis integration.
    *   Check `crates/ruff_linter/src/checkers/ast/analyze/bindings.rs` for potential updates related to global/nonlocal status checks.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.