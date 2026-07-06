## Description

Biome already validates ARIA attribute values in JSX-based files, but it does not do so for plain HTML files or component templates used in frameworks like Vue, Svelte, and Astro. This means developers writing HTML markup or component templates can accidentally use incorrect values for accessibility attributes — for example, using "yes" instead of "true" for a boolean state, or supplying a non-numeric string where a number is expected — without getting any feedback from the linter.

## Expected Behavior

- A new accessibility lint rule should validate the values of ARIA state and property attributes in HTML and component template files.
- When an ARIA attribute has an invalid value, a diagnostic should be emitted indicating which attribute is wrong and what valid values are accepted according to the WAI-ARIA specification.
- The rule should support different ARIA value types: booleans, tristate, token enumerations, ID reference lists, and numeric values.
- Attribute name matching in plain HTML should be case-insensitive (e.g., an uppercase attribute name and its lowercase equivalent should be treated the same way). The diagnostic should preserve the attribute name exactly as written in source.
- A valueless boolean ARIA attribute (the attribute present but without a value) should be treated as equivalent to the value "true" and accepted when "true" is valid for that attribute type.
- Dynamic/runtime-bound ARIA attributes in Vue templates should be skipped, since their values cannot be known at analysis time.
- The rule should apply to HTML, Vue, Svelte, and Astro files.

## Why This Matters

Without this validation, incorrect ARIA attribute values can silently ship to production, breaking the experience for users relying on assistive technologies. Having the linter catch these errors early helps teams maintain accessible, spec-compliant markup across all file types they author.
