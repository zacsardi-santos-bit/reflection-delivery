## Description

CSS selectors that chain many class selectors together can quickly become hard to read, hard to override, and hard to reuse. However, the CSS linter currently provides no rule to enforce a maximum number of class selectors in a single selector. Teams that want to keep their stylesheets maintainable have no automated way to catch selectors that exceed their chosen complexity threshold.

## Expected Behavior

- A new lint rule in the nursery group should detect when a CSS selector contains more class selectors than a configurable maximum.
- The rule should support a configurable option that lets teams choose their own threshold — including zero (to disallow class selectors entirely), one, two, or any other limit.
- The total class count should include class selectors that appear inside pseudo-class function arguments.
- Each selector in a comma-separated list should be evaluated independently.
- Nested CSS selectors (using the nesting syntax) should be evaluated independently from their parent selectors.
- Selectors with dynamic interpolations (as found in SCSS) should be excluded from the check.
- The rule should produce no warnings at all when no threshold has been explicitly configured — it should only activate when a limit is set.

## Why This Matters

Without this rule, large CSS codebases can accumulate overly specific selectors that are difficult to maintain, override, and understand. Automating the enforcement of a class-selector limit helps teams catch complexity issues early in the development workflow.
