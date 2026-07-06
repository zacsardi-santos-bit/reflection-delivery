Implement a new lint rule in the JavaScript analyzer to enforce explicit comparisons when checking the length or size of a collection. Ensure that the rule flags implicit boolean checks and suggests clearer alternatives, using greater-than-zero for non-empty checks and strict equality to zero for empty checks.

*   Implement the `useExplicitLengthCheck` rule in the `nursery` category:
    *   Create the rule in `crates/biome_js_analyze/src/lint/nursery/use_explicit_length_check.rs` with the struct named `UseExplicitLengthCheck`.
    *   Declare the rule using the `declare_rule!` macro.
    *   Register the rule in `crates/biome_js_analyze/src/lint/nursery.rs` and the diagnostic category `lint/nursery/useExplicitLengthCheck` in `crates/biome_diagnostics_categories/src/categories.rs`.

*   Ensure the rule flags and provides fixes for:
    *   Implicit boolean use of `.length` or `.size`, suggesting 'Use .length > 0 when checking .length is not zero.' and 'Use .length === 0 when checking .length is zero.'
    *   Negated checks like `!foo.length`, suggesting 'Replace .length with .length === 0'.
    *   Reversed operand forms like `0 === foo.length`, correcting to `foo.length === 0`.
    *   Patterns like `foo.length < 1` and `foo.length >= 1`, suggesting explicit comparisons.
    *   Double-negation patterns and `Boolean()` calls, simplifying them to explicit checks.
    *   Logical expressions like `Boolean(foo.length || bar)`, only flagging the `.length` portion.

*   Handle special cases:
    *   Insert whitespace when a negated `.length` check follows a keyword without a separator.
    *   Do not flag non-relevant cases such as non-boolean contexts, computed member accesses, or already-explicit comparisons.

*   Apply the rule to `this.length` and `this.size` inside class methods.

*   Create test fixtures:
    *   `invalid.js` and `invalid.js.snap` for invalid patterns in `crates/biome_js_analyze/tests/specs/nursery/useExplicitLengthCheck/`.
    *   `valid.js` and `valid.js.snap` for valid patterns in the same directory.

*   Register the rule in the nursery rules list for test discovery under the test name `use_explicit_length_check`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.