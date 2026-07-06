I'm working on the SCSS parser and running into some issues with how query feature conditions are parsed when variable interpolation is involved.

*   When a SCSS container query uses interpolation for both the left and right bounds of a range comparison (e.g., interpolated variable <= property <= interpolated variable), the parser must accept it as valid and produce a CssQueryFeatureRangeInterval AST node with ScssInterpolation nodes for both the left and right fields — with no parse errors.

*   When a media query range comparison has an SCSS interpolation on the left side and a property identifier on the right side, the parser must classify it as CssQueryFeatureReverseRange (CST: CSS_QUERY_FEATURE_REVERSE_RANGE), not CssQueryFeatureRange.

*   When a plain query feature uses an SCSS interpolated identifier as its name, followed by a colon but no value (e.g., @media (#{$feature}:)), the parser must produce a CssQueryFeaturePlain AST node where the value field is 'missing (required)', and emit a parse error with message 'Unexpected value or character.' listing the expected types: identifier, string, number, dimension, ratio, custom property, function.

*   When a range interval query has SCSS interpolation as the left value and a comparison operator is present but the right value is missing (e.g., @container (#{$min} <= property <=)), the parser must produce a CssQueryFeatureRangeInterval with the right field as 'missing (required)', and emit a parse error with message 'Unexpected value or character.' listing the same expected types: identifier, string, number, dimension, ratio, custom property, function.

*   The parser must recover gracefully from the two error cases above — after reporting errors, it must continue parsing the rest of the rule block (the declarations inside the at-rule) without crashing or producing further cascading errors.


*   Interface details: The tests validate the CSS/SCSS parser's AST output via snapshot comparison. The implementation must produce specific AST and CST node types by name. There are no new public Rust functions or classes with explicit signatures to implement; the required changes are to the parser's grammar rules and node type definitions.

The following AST/CST node types must exist and be produced correctly:

**Node Type: CssQueryFeatureReverseRange**
- Location: CSS parser grammar definitions (biome_css_parser crate)
- Description: Represents a CSS/SCSS query feature range where the comparison is expressed in reversed order — the interpolated or computed value appears on the LEFT side and the property name appears on the RIGHT side. This is distinct from `CssQueryFeatureRange`. The corresponding CST token kind is `CSS_QUERY_FEATURE_REVERSE_RANGE`.
- The AST node must contain: `left` (the left-side value, e.g., a `ScssInterpolatedIdentifier`), `left_comparison` (`CssQueryFeatureRangeComparison`), and `name` (the property identifier on the right).

**Node Type: CssQueryFeatureRangeInterval (with SCSS interpolation support)**
- Location: CSS parser grammar definitions (biome_css_parser crate)
- Description: Represents a range interval query feature with a lower bound, a property name, and an upper bound. The `left` and `right` fields must accept `ScssInterpolation` nodes (not just plain identifiers or numbers). The corresponding CST token kind is `CSS_QUERY_FEATURE_RANGE_INTERVAL`.
- Fields: `left` (accepts `ScssInterpolation`), `left_comparison` (`CssQueryFeatureRangeComparison`), `name` (`CssIdentifier`), `right_comparison` (`CssQueryFeatureRangeComparison`), `right` (accepts `ScssInterpolation`; marked `missing (required)` on error).

**Error Message: "Unexpected value or character."**
- When a query feature is missing a required value (after a colon in a plain feature or after a comparison operator in a range interval), the parser must emit a parse error with the message: `Unexpected value or character.`
- The error must include a hint listing the expected token kinds: `identifier`, `string`, `number`, `dimension`, `ratio`, `custom property`, `function`

**Snapshot file locations (must match exactly):**
- `crates/biome_css_parser/tests/css_test_suite/ok/scss/at-rule/query-feature-interpolation.scss` — new valid SCSS input file
- `crates/biome_css_parser/tests/css_test_suite/ok/scss/at-rule/query-feature-interpolation.scss.snap` — snapshot for the valid case
- `crates/biome_css_parser/tests/css_test_suite/error/scss/at-rule/query-feature-interpolation.scss` — new invalid SCSS input file
- `crates/biome_css_parser/tests/css_test_suite/error/scss/at-rule/query-feature-interpolation.scss.snap` — snapshot for the error cases
- `crates/biome_css_parser/tests/css_test_suite/ok/scss/at-rule/media-interpolation.scss.snap` — existing snapshot, updated to use `CssQueryFeatureReverseRange`


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.