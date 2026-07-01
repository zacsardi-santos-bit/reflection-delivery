Implement automatic fix suggestions for the exhaustive-dependencies lint rule in React hooks. Ensure the rule provides unsafe fixes for missing, extra, or duplicate dependencies and handles syntax errors gracefully during validation.

*   Update the exhaustive-dependencies lint rule:
    *   Mark diagnostics as fixable by including a 'FIXABLE' label in the diagnostic header.
    *   Emit an unsafe code action with the message "Unsafe fix: Add the missing dependencies to the list." when a dependency array is missing entries.
    *   Emit an unsafe code action with the message "Unsafe fix: Remove the extra dependencies from the list." when a dependency array has extra or duplicate entries.
    *   Classify all fix actions as unsafe fixes.
    *   Ensure fixes apply correctly to all hook variants: useEffect, useCallback, useMemo, useLayoutEffect, useInsertionEffect, useImperativeHandle, and equivalent Preact/custom hooks.

*   Modify test infrastructure:
    *   Rename the test specification file for invalid missing-dependency cases to `missingDependenciesInvalid.jsx`.
    *   Ensure the snapshot file `missingDependenciesInvalid.jsx.snap` includes FIXABLE markers and Unsafe fix sections for each diagnostic.
    *   Update the `check_code_action` function in `crates/biome_js_analyze/tests/spec_tests.rs`:
        *   Accept an additional parameter of type `&AnyJsRoot` representing the original parsed root.
        *   Skip bogus-node checks and re-parse validation when `has_bogus_nodes_or_empty_slots(root.syntax())` returns true.
    *   Ensure all call sites of `check_code_action` inside `analyze_and_snap` pass the original root as the additional argument.

*   Ensure constants for diagnostic messages are defined:
    *   "Unsafe fix: Add the missing dependencies to the list." in `crates/biome_js_analyze/src/lint/correctness/use_exhaustive_dependencies.rs`.
    *   "Unsafe fix: Remove the extra dependencies from the list." in the same location.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.