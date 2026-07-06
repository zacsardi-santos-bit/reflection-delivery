I'm working on the Biome linter and I'd like to add a new accessibility lint rule that validates ARIA attribute values in HTML and component template files (Vue, Svelte, Astro).

*   A lint rule named 'useValidAriaValues' must be implemented in the biome_html_analyze crate under the a11y category. The rule must be declared with the name 'useValidAriaValues' and language 'html' so that the spec test runner can discover it.

*   The rule must apply to HTML, Astro, Svelte, and Vue template files, detecting statically written ARIA attribute values that violate the WAI-ARIA specification.

*   When an invalid ARIA attribute value is found, the rule must emit a diagnostic with the message: 'The value of the ARIA attribute {attr} is not correct.' where {attr} is the attribute name as written in source.

*   All diagnostics must include a trailing note: 'Use a valid value for the {attr} attribute according to the WAI-ARIA specification.'

*   For boolean ARIA attributes (aria-hidden and similar), invalid values must produce a footer list: 'The only supported values for the {attr} property are:' with items: undefined, false, true.

*   For tristate ARIA attributes (aria-checked and similar), invalid values must produce a footer list: 'The only supported values for the {attr} property are:' with items: false, true, mixed.

*   For ID reference list ARIA attributes (aria-labelledby and similar), an empty string or invalid value must produce a note: 'The only supported value is a space-separated list of HTML identifiers.' Empty strings must be flagged.

*   For numeric ARIA attributes (aria-valuemax, aria-valuemin and similar), non-numeric values must produce a note: 'The only supported value is a number.'

*   For token ARIA attributes (aria-orientation and similar), invalid token values must produce a footer list: 'The only supported value for the {attr} property is one of the following:' with the valid tokens (e.g., undefined, horizontal, vertical for aria-orientation).

*   In HTML files, ARIA attribute name matching must be case-insensitive. An attribute like 'ARIA-HIDDEN' or 'Aria-Checked' must be recognized as an ARIA attribute. The diagnostic message must preserve the original casing as written in source.

*   Valueless ARIA attributes (written without any value, e.g., <div aria-hidden>) must be treated as having the value 'true'. If 'true' is a valid value for that attribute type (e.g., boolean or tristate), no diagnostic must be emitted. If the attribute type does not include 'true' as a valid token (e.g., aria-orientation), a diagnostic must be emitted.

*   In Vue template files, dynamically bound ARIA attributes (using ':aria-*' shorthand or 'v-bind:aria-*' directive syntax) must be skipped entirely — no diagnostic must be emitted for these dynamic bindings.

*   Valid ARIA attribute values must never produce diagnostics: 'true'/'false' for boolean types, 'true'/'false'/'mixed' for tristate, valid tokens for token types, non-empty space-separated identifiers for ID reference list, and numeric strings for number/integer types.


*   Interface details: Type: Rule (Rust lint rule via `declare_lint_rule!`)
Name: UseValidAriaValues
Location: crates/biome_html_analyze/src/lint/a11y/use_valid_aria_values.rs
Description: An accessibility lint rule that validates ARIA attribute values in HTML and component template files. Must be declared with name "useValidAriaValues", language "html", under the `lint/a11y` module path, and registered in the biome_html_analyze crate's rule registry.

Diagnostic output format (must match exactly for snapshot tests to pass):

Main error message:
  "The value of the ARIA attribute {attr} is not correct."

Where {attr} is the attribute name exactly as written in source (preserving case).

Type-specific footer/note messages (appended after main error, before the WAI-ARIA note):
- Boolean (e.g. aria-hidden):       footer list "The only supported values for the {attr} property are:" → [undefined, false, true]
- Tristate (e.g. aria-checked):     footer list "The only supported values for the {attr} property are:" → [false, true, mixed]
- Number (e.g. aria-valuemax):      note "The only supported value is a number."
- IdReferenceList (e.g. aria-labelledby): note "The only supported value is a space-separated list of HTML identifiers."
- Token (e.g. aria-orientation):    footer list "The only supported value for the {attr} property is one of the following:" → [token1, token2, ...]

Final note (always appended last):
  "Use a valid value for the {attr} attribute according to the WAI-ARIA specification."

Behavioral requirements:
- In HTML files (.html), ARIA attribute name matching is case-insensitive; in Vue/Svelte/Astro, case-sensitive.
- Valueless ARIA attributes are treated as having value "true".
- Vue dynamic binding syntax (:aria-* or v-bind:aria-*) is skipped (no diagnostic emitted).
- The rule must be registered so the spec test runner discovers it by its declared name "useValidAriaValues" when running tests in crates/biome_html_analyze/tests/specs/a11y/useValidAriaValues/.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.