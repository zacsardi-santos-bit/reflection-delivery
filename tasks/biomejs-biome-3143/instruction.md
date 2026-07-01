Implement a new lint rule in the Biome JavaScript analyzer's nursery category to validate the `autocomplete` attribute on HTML input elements. Ensure the rule flags invalid, unknown, or improperly ordered autocomplete tokens and supports configuration for custom input components. Skip validation for dynamic expressions or types, and ensure the rule is properly registered and tested.

*   Create a lint rule in the nursery category:
    *   Validate `autocomplete` attribute values on HTML input elements and specified custom components.
    *   Accept a configuration option `inputComponents` (array of strings) for custom JSX component names.
    *   Emit a diagnostic message: "Use valid values for the autocomplete attribute."
    *   Include notes with links to WCAG 1.3.5, HTML Living Standard, and MDN documentation.
    *   Skip diagnostics for absent, boolean, or dynamic `autocomplete` values, and dynamic `type` attributes.

*   Implement valid and invalid value checks:
    *   Valid single-token values: empty string, "off", "on", "section-*", and HTML autofill specification tokens.
    *   Valid multi-token values: optional "section-*", followed by "billing" or "shipping" with valid tokens.
    *   Trigger diagnostics for unknown tokens, invalid sequences, and combinations like "home url".

*   File and struct requirements:
    *   Implementation file: `crates/biome_js_analyze/src/lint/nursery/use_valid_autocomplete.rs`.
    *   Declare a public rule struct `UseValidAutocomplete` and options struct `UseValidAutocompleteOptions`.
    *   Register the rule in `crates/biome_js_analyze/src/lint/nursery.rs` and diagnostics category in `crates/biome_diagnostics_categories/src/categories.rs`.
    *   Add a type alias in `crates/biome_js_analyze/src/options.rs`.
    *   Update `Nursery` struct in `crates/biome_configuration/src/linter/rules.rs`.

*   Testing:
    *   Include a unit test `test_order` to verify sorted lookup tables are in ascending order for binary search efficiency.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.