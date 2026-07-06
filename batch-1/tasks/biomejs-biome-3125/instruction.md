Implement a new lint rule named `noSubstr` in the `nursery` group to flag the usage of the `substr` and `substring` string methods in JavaScript, recommending `slice` as a replacement. Ensure the rule provides specific diagnostic messages and handles optional chaining patterns, offering an unsafe auto-fix for zero-argument calls.

Requirements:

*   Create a lint rule in `crates/biome_js_analyze/src/lint/nursery/no_substr.rs`:
    *   Name: `NoSubstr`
    *   Type: Rule (Rust struct)
    *   Description: Flags use of `substr` and `substring` methods, recommending `slice`.

*   Diagnostic Messages:
    *   For `substr`: "Avoid using substr and consider using slice instead."
    *   For `substring`: "Avoid using substring and consider using slice instead."
    *   Info note 1: "slice is more commonly used and has a less surprising behavior."
    *   Info note 2: "See MDN web docs for more details."

*   Auto-fix:
    *   Label: "Use .slice() instead."
    *   Applicability: Unsafe
    *   Provided only for method calls with zero arguments.
    *   Replace method name with `slice`, preserving optional chaining operators.
    *   No fix for:
        *   Bare property accesses (not followed by a call).
        *   Calls with one or more arguments.

*   Handle optional chaining patterns:
    *   Flag and provide auto-fix for zero-argument calls in patterns like `obj?.method()`, `obj.method?.()`, `obj?.prop?.method()`, `obj?.prop.method()`, and `obj.prop?.method()`.

*   Ensure calls to `.slice()` with any number of arguments are not flagged.

*   Register the rule in the nursery group:
    *   Diagnostic category: `lint/nursery/noSubstr`.

*   Create test spec files:
    *   `crates/biome_js_analyze/tests/specs/nursery/noSubstr/invalid.js`
    *   `crates/biome_js_analyze/tests/specs/nursery/noSubstr/invalid.js.snap`
    *   `crates/biome_js_analyze/tests/specs/nursery/noSubstr/valid.js`
    *   `crates/biome_js_analyze/tests/specs/nursery/noSubstr/valid.js.snap`

*   Ensure the additional snapshot file exists:
    *   `crates/biome_js_analyze/tests/specs/nursery/useTrimStartEnd/valid.js.snap`

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.