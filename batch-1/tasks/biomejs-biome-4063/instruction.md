Implement a new CSS lint rule named 'useSortedProperties' to enforce a consistent, semantic ordering of properties within CSS rule blocks. Ensure that the rule detects unordered properties and provides an automatic fix to reorder them according to specified categories. Handle special cases like vendor prefixes, shorthand properties, and nested rule blocks.

*   Implement the 'useSortedProperties' lint rule in the file `crates/biome_css_analyze/src/lint/nursery/use_sorted_properties.rs`.
    *   Declare the module as `pub mod use_sorted_properties;` in `crates/biome_css_analyze/src/lint/nursery.rs`.
    *   Register `self::use_sorted_properties::UseSortedProperties` in the `declare_lint_group!` macro.

*   Ensure the rule emits a diagnostic message "Properties can be sorted." with the note "Consistently ordering CSS properties can improve readability."
    *   Provide a safe auto-fix labeled "Sort these properties" to reorder properties.

*   Implement property sorting using the following semantic order:
    *   Custom properties (CSS variables prefixed with '--')
    *   Layout properties (e.g., display, flex, grid)
    *   Spacing properties (e.g., margin, padding)
    *   Typography properties (e.g., color, font)
    *   Interactive behavior properties (e.g., pointer-events)
    *   Decorative styles (e.g., border, background)
    *   Transitions and animations

*   Ensure nested rules and at-rules are placed after all property declarations within the same block.

*   Handle vendor-prefixed properties:
    *   Sort vendor-prefixed variants before their non-prefixed counterparts.
    *   Use the order: '-moz-' before '-webkit-' before unprefixed.

*   Ensure property name comparisons are case-insensitive.

*   Maintain stable sorting:
    *   Preserve the original relative order of duplicate property declarations.

*   Do not flag shorthand properties following their longhand sub-properties as violations, unless they have different vendor prefixes.

*   Operate recursively to check and fix nested rule blocks independently.

*   Skip sorting and do not emit diagnostics for blocks with unknown or unparseable property declarations.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.