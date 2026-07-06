Implement a new CSS lint rule in the Biome CSS analyzer to detect and flag the use of unknown or invalid CSS units. Ensure the rule checks all relevant CSS contexts and handles special cases as specified.

*   Implement the rule as a Rust struct named `NoUnknownUnit` in `crates/biome_css_analyze/src/lint/nursery/no_unknown_unit.rs`.
    *   Declare the rule using the `declare_rule!` macro with `name: "noUnknownUnit"` and `recommended: true`.
    *   Implement the `Rule` trait with `type Query = Ast<AnyCssDimension>`.
*   Register the module in `crates/biome_css_analyze/src/lint/nursery.rs`:
    *   Declare the module as `pub mod no_unknown_unit;`.
    *   Add the rule to the `declare_group!` macro as `self::no_unknown_unit::NoUnknownUnit`.
*   Add a type alias in `crates/biome_css_analyze/src/options.rs`:
    *   Use the pattern: `pub type NoUnknownUnit = <lint::nursery::no_unknown_unit::NoUnknownUnit as biome_analyze::Rule>::Options;`.
*   Register the diagnostic category in `crates/biome_diagnostics_categories/src/categories.rs`:
    *   Add `"lint/nursery/noUnknownUnit": "https://biomejs.dev/linter/rules/no-unknown-unit"` to the `define_categories!` macro.
*   Register the rule in `crates/biome_configuration/src/linter/rules.rs`:
    *   Add a field `pub no_unknown_unit: Option<RuleConfiguration<NoUnknownUnit>>` to the `Nursery` struct with the doc comment `Disallow unknown CSS units.`.
    *   Include `"noUnknownUnit"` in `GROUP_RULES`, `RECOMMENDED_RULES`, `RECOMMENDED_RULES_AS_FILTERS`, `ALL_RULES_AS_FILTERS`, and the enabled/disabled rule index set methods.
    *   Ensure the `get_rule_configuration` method handles `"noUnknownUnit"`.
*   Emit a diagnostic when encountering an unrecognized unit:
    *   Use the message `Unexpected unknown unit: {unit}` where `{unit}` is the unit text as it appears in the source.
    *   Include a note `See MDN web docs for more details.` with a hyperlink to `https://developer.mozilla.org/en-US/docs/Learn/CSS/Building_blocks/Values_and_units#lengths`.
    *   Add a footer list `Use a known unit instead, such as:` with items: `px`, `em`, `rem`, `etc.`.
*   Ensure the rule flags unknown units in all CSS contexts:
    *   Property values, function arguments, CSS custom property values, media query feature values, and vendor-prefixed property values.
    *   Handle units case-insensitively.
    *   Flag numbers in scientific notation with invalid unit suffixes.
    *   Special handling for the `x` unit, allowing it only in resolution contexts.
*   Do not flag units in:
    *   CSS comments, string values, `url()` function arguments, CSS variable references, preprocessor variable references, selector names, pseudo-class/pseudo-element names, attribute values, or CSS property names.
*   Allow all standard CSS units case-insensitively, including: `em`, `ex`, `px`, `cm`, `mm`, `in`, `pt`, `pc`, `ch`, `rem`, `vh`, `vw`, `vmin`, `vmax`, `s`, `ms`, `deg`, `grad`, `turn`, `rad`, `fr`, `%`, `ic`, and unitless numbers.
*   Flag trailing hex characters in unicode range values interpreted as dimension unit suffixes if not valid CSS units.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.