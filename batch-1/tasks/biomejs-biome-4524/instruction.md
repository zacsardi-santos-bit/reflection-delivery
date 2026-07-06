Implement a new lint rule in Biome's nursery category to enforce correct usage of the `parseInt` and `Number.parseInt` functions in JavaScript. Ensure the rule flags calls missing a radix argument, calls with an invalid radix, and calls with no arguments, providing specific diagnostics and an automatic fix where applicable.

*   Define the lint rule as `lint/nursery/useParseIntRadix` to detect problematic uses of `parseInt` and `Number.parseInt`.
*   Emit a diagnostic with the message "Missing radix parameter" when the function is called with only one argument.
    *   Include the information text "Add a non-fractional number between 2 and 36".
    *   Mark this diagnostic as FIXABLE with an unsafe fix labeled "Add a radix of 10" that appends `, 10` to the call.
*   Emit a diagnostic with the message "Invalid radix parameter" when the radix argument is invalid:
    *   Invalid cases include: a string, a value outside the range 2–36, `undefined`, a BigInt literal, or more than two arguments.
    *   Include the information text "Radix must be a non-fractional number between 2 and 36".
    *   Do not provide a fix for this diagnostic.
*   Emit a diagnostic with the message "This call to parseInt has no arguments, it will always return NaN" when the function is called with no arguments.
    *   Include the information text "Add arguments to this function call".
    *   Do not provide a fix for this diagnostic.
*   Ensure no diagnostics are emitted for calls with a valid integer radix (2–36), including those using numeric separators.
*   Ignore calls to functions other than `parseInt` and `Number.parseInt`.

Interface:
*   Define a struct `UseParseIntRadix` in `crates/biome_js_analyze/src/lint/nursery/use_parse_int_radix.rs`.
*   Use the `declare_lint_rule!` macro with name "useParseIntRadix", language "js", and `fix_kind: FixKind::Unsafe`.
*   Implement the `Rule` trait with appropriate parameters.

Registration:
*   Add `pub(crate) mod use_parse_int_radix;` in `crates/biome_js_analyze/src/lint/nursery.rs`.
*   Add `self::use_parse_int_radix::UseParseIntRadix,` to the `declare_lint_group!` rules list for the Nursery group.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.