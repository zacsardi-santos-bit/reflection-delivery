I'd like to add a new CSS lint rule to the nursery group that flags selectors with too many class selectors.

*   The rule `noExcessiveSelectorClasses` must be registered in the `nursery` group of the CSS linter and must be configurable via a `maxClasses` option (JSON name) stored as `max_classes: Option<u16>` in the options struct. When `maxClasses` is not configured (i.e., the option is absent or null), the rule must produce no diagnostics at all — even for selectors with many class selectors.

*   When `maxClasses` is set to 1, any individual selector containing 2 or more total class selectors must trigger a diagnostic. This includes selectors where classes appear on the same element, in descendant chains, or in combinations with element selectors.

*   When `maxClasses` is set to 0, any individual selector containing 1 or more class selectors must trigger a diagnostic.

*   When `maxClasses` is set to 2, any individual selector containing 3 or more total class selectors must trigger a diagnostic.

*   Class selectors appearing inside pseudo-class function arguments (such as inside :not(), :is(), :has(), and :nth-child(... of ...)) must be counted as part of the total class selector count for the enclosing top-level selector. For example, a selector with 2 outer classes and a :not() argument with 2 inner classes counts as 4.

*   Comma-separated selector lists must be evaluated independently: each individual selector in the list is checked against the `maxClasses` threshold on its own and may produce its own separate diagnostic.

*   Nested CSS selectors (using the CSS nesting `&` combinator) must be evaluated independently from their parent selectors — each nested rule's selectors are checked against the threshold on their own, not combined with the parent's class count.

*   SCSS selectors that contain an interpolation (a dynamic expression such as `#{$variable}` embedded in the selector) must not be flagged by this rule, even if they contain class selectors.

*   When the rule triggers a diagnostic, the main message must be exactly: 'Expected this selector to have no more than {N} class selector, but found {M}.' when N equals 1 (singular), or 'Expected this selector to have no more than {N} class selectors, but found {M}.' when N is any other value (plural).

*   The rule must also emit a note: 'Selectors with too many chained classes are harder to read, override, and reuse.'

*   The rule must also emit a note: 'Reduce the number of class selectors in this selector, or split it into simpler selectors.'

*   The diagnostic span must cover the full text range of the offending selector. Each violating selector in a comma-separated list produces its own separate diagnostic with its own span.


*   Interface details: Type: Rule (Rust struct)
Name: NoExcessiveSelectorClasses
Location: crates/biome_css_analyze/src/lint/nursery/no_excessive_selector_classes.rs
Description: A CSS lint rule that flags individual selectors containing more class selectors than the configured maximum. The rule must be registered in the nursery group and exported from the nursery lint module so the test framework can discover it by the name "noExcessiveSelectorClasses". The rule struct must use the `NoExcessiveSelectorClassesOptions` type as its Options associated type.

Type: Options Struct
Name: NoExcessiveSelectorClassesOptions
Location: crates/biome_rule_options/src/no_excessive_selector_classes.rs
Description: Holds the rule's configuration. Must have a `max_classes` field of type `Option<u16>` serialized as `maxClasses` in JSON (serde camelCase rename). The field must be optional — when it is `None` (not configured), the rule must not fire at all (no diagnostics are produced). When set to a value, the rule flags selectors whose total class count exceeds that value.
Signature: pub struct NoExcessiveSelectorClassesOptions { pub max_classes: Option<u16> }

Note on registration: The module must be declared in `crates/biome_rule_options/src/lib.rs` as `pub mod no_excessive_selector_classes;`. The rule struct must be registered in the nursery lint module so that running tests for `biome_css_analyze` with the filter `no_excessive_selector_classes` discovers and exercises the rule.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.