Implement a new lint rule in TypeScript to enforce consistent accessibility modifier usage on class members. Configure the rule to support three modes: requiring explicit modifiers, disallowing redundant public modifiers, or forbidding all modifiers. Ensure the rule applies to all class member types and provides clear diagnostic messages for violations.

*   Name the rule `useConsistentMemberAccessibility` and place it in the nursery lint group.
    *   Reference diagnostics as `lint/nursery/useConsistentMemberAccessibility`.
*   Accept a configuration option key `accessibility` with values: `"explicit"`, `"noPublic"`, or `"none"`.
    *   Default to `"noPublic"`.
*   Implement mode-specific behavior:
    *   **No-public mode**:
        *   Flag members with the `public` modifier.
        *   Error: 'The public modifier is disallowed.' (with 'public' emphasized).
        *   Hint: 'Remove the accessibility modifier.'
    *   **Explicit mode**:
        *   Flag members missing an accessibility modifier.
        *   Error: 'Missing accessibility modifier on this member.'
        *   Hint: 'Use public to explicitly make a member public.' (with 'public' emphasized).
        *   Flag native private class fields using `#` syntax as missing modifiers.
    *   **None mode**:
        *   Flag members with any accessibility modifier.
        *   Error: 'Accessibility modifiers are disallowed.'
        *   Hint: 'Remove the accessibility modifier.'
*   Apply the rule to all member types:
    *   Class properties, methods, constructors, getter/setters, constructor parameter shorthands, and their abstract/signature counterparts.
*   Ensure `Accessibility` enum variants serialize to/from JSON strings `"noPublic"`, `"explicit"`, and `"none"`.
*   Declare the rule struct `UseConsistentMemberAccessibility` in `crates/biome_js_analyze/src/lint/nursery/use_consistent_member_accessibility.rs`.
    *   Use `declare_lint_rule!` with the name `"useConsistentMemberAccessibility"` and language `"ts"`.
    *   Register as `pub mod use_consistent_member_accessibility` in `crates/biome_js_analyze/src/lint/nursery.rs`.
    *   Include in `declare_lint_group!`.
*   Define `ConsistentMemberAccessibilityOptions` struct with a field `accessibility: Accessibility`.
    *   Make it public, derive `Deserializable`, and ensure it is serializable.
*   Define `Accessibility` enum with variants `NoPublic`, `Explicit`, `None`.
    *   Make it public, derive `Deserializable`, and set `NoPublic` as default.
*   Create a type alias `UseConsistentMemberAccessibility` in `crates/biome_js_analyze/src/options.rs`.
*   Register the rule in `crates/biome_configuration/src/analyzer/linter/rules.rs`.
    *   Add `use_consistent_member_accessibility` to the `Nursery` struct.
    *   Include `"useConsistentMemberAccessibility"` in `GROUP_RULES` and filter arrays.
*   Add the category `"lint/nursery/useConsistentMemberAccessibility"` in `crates/biome_diagnostics_categories/src/categories.rs`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.