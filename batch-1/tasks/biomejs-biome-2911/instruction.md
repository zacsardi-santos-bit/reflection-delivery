Implement a new CSS lint rule to detect invalid direction values in gradient functions. Ensure the rule flags non-standard syntax in standard and vendor-prefixed gradient functions, while allowing valid syntax and color values. Register and configure the rule within the biome system.

*   Implement the lint rule named 'noInvalidDirectionInLinearGradient' in the nursery group.
    *   Use the diagnostic category path 'lint/nursery/noInvalidDirectionInLinearGradient'.
*   For standard `linear-gradient()` functions:
    *   Flag bare directional keywords (top, bottom, left, right) without the 'to' prefix as invalid.
    *   Flag angle values without a unit suffix (e.g., 45, 0.25) as invalid.
    *   Flag malformed direction strings, such as 'topin' or 'to top top'.
*   For vendor-prefixed gradient functions (`-webkit-linear-gradient`, `-moz-linear-gradient`, `-o-linear-gradient`, `-ms-linear-gradient`):
    *   Flag 'to <direction>' syntax as invalid.
    *   Consider bare directional keywords without the 'to' prefix as valid.
*   Do not flag:
    *   First arguments starting with a color value.
    *   Directions containing color interpolation modifiers (e.g., 'in srgb to top').
*   Apply the rule to gradient functions within multi-value background properties.
*   When a violation is detected, include:
    *   An error-level message: 'Unexpected nonstandard direction'.
    *   A note: 'You should fix the direction value to follow the syntax.'
    *   A note with a hyperlink: 'See MDN web docs for more details.'
*   Register the rule in the nursery lint group module and diagnostics categories registry.
*   Expose the rule via the linter configuration and options type system.
*   Declare the module with `pub mod no_invalid_direction_in_linear_gradient;` in `crates/biome_css_analyze/src/lint/nursery.rs`.
*   Register the rule in the `declare_lint_group!` macro as `self::no_invalid_direction_in_linear_gradient::NoInvalidDirectionInLinearGradient`.
*   Add a diagnostic category entry in `crates/biome_diagnostics_categories/src/categories.rs`.
*   Add a configuration field in `crates/biome_configuration/src/linter/rules.rs`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.