Implement a new lint rule in the nursery category of biome that enforces adjacent placement of overloaded function and method signatures in TypeScript. Ensure that all overloads for the same name are grouped together without unrelated members in between across various TypeScript contexts.

*   Implement a lint rule named `UseAdjacentOverloadSignatures` in `crates/biome_js_analyze/src/lint/nursery/use_adjacent_overload_signatures.rs`.
    *   Use the `declare_rule!` macro with the name "useAdjacentOverloadSignatures", language "js", and recommended: false.
    *   Implement the `Rule` trait for this struct.
*   Ensure the rule detects non-adjacent overloads in:
    *   Namespace declarations (`TsDeclareStatement` for declare namespace bodies)
    *   Type alias object types (`TsTypeAliasDeclaration`)
    *   Interface declarations (`TsInterfaceDeclaration`)
    *   Class declarations (`JsClassDeclaration`)
    *   Top-level exported function declarations (`JsModule`)
    *   Function return type object annotations (`JsFunctionDeclaration`)
*   Emit a diagnostic with the message: "All {name} signatures must be adjacent." where `{name}` is the method/function name.
    *   Use error severity for the primary diagnostic.
    *   Use informational severity for additional violations within the same container.
*   Register the diagnostic category `lint/nursery/useAdjacentOverloadSignatures` in `crates/biome_diagnostics_categories/src/categories.rs` using the `define_categories!` macro with an appropriate documentation URL.
*   Use a gap-position algorithm to determine the specific occurrence to flag:
    *   Sort positions of all occurrences in member order.
    *   Compute the full contiguous range from min to max position.
    *   Identify the first actual position that diverges from the expected contiguous range.
    *   Flag the actual occurrence immediately before the divergence if the expected position falls in the lower half of the range.
    *   Flag the actual occurrence at the divergence index if it falls in the upper half.
*   Register the rule module in `crates/biome_js_analyze/src/lint/nursery.rs` with `pub mod use_adjacent_overload_signatures;`.
*   Include `UseAdjacentOverloadSignatures` in the `declare_group!` macro invocation with other nursery rules.
*   Ensure no diagnostics are produced for valid code where all overloads are grouped consecutively.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.