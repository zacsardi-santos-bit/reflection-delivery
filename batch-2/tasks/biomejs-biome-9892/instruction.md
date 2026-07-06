I've been reviewing the diagnostic messages produced by several lint rules in our codebase and found that many of them are not very informative.

*   For the CSS lint rule that detects shorthand property overrides, the primary error message must read: "This shorthand property {shorthand} overrides the earlier {longhand} declaration." (where {shorthand} and {longhand} are the actual property names). Two additional note messages must follow: "Shorthand properties reset related longhand properties, which can overwrite earlier values unexpectedly." and "Declare the shorthand first, or use longhand properties consistently so later declarations stay explicit."

*   For the GraphQL lint rule that disallows certain root types, the info message must read: "This schema defines the disallowed root type {name}." (where {name} is the root type name). The existing single hint message must be replaced with two separate notes: "This project forbids that root type to enforce a specific schema design." and "Use a different root type, or update the rule configuration if this root type should be allowed."

*   For the HTML lint rule that detects misuse of the autofocus attribute, the primary error message must read: "This element uses the autofocus attribute outside an allowed modal context." A new info note must be added before the existing fix suggestion: "Autofocusing elements can disrupt navigation and confuse screen reader and keyboard users."

*   For the JavaScript lint rule that detects self-comparisons, the primary error message must read: "This comparison uses the same expression on both sides." Two additional note messages must follow every violation: "Self-comparisons are usually redundant or a sign that the wrong value is being compared." and "Compare two different values instead, or use Number.isNaN() if you are checking for NaN."

*   For the JavaScript lint rule that detects use of a property named 'then' on objects, the primary error message must read: "This object defines a then property." Two note messages must follow: "Values with a then property can be treated like promises by await and dynamic imports, which can cause unexpected behavior." and "Rename this property to something other than then unless you intentionally need a thenable object."

*   For the JavaScript lint rule that detects use of a member named 'then' on classes, the primary error message must read: "This class defines a then member." Two note messages must follow: "Values with a then property can be treated like promises by await and dynamic imports, which can cause unexpected behavior." and "Rename this member to something other than then unless you intentionally need a thenable class."

*   For the JavaScript lint rule that detects exporting the name 'then', the primary error message must read: "This export exposes the name then." Two note messages must follow: "Values with a then property can be treated like promises by await and dynamic imports, which can cause unexpected behavior." and "Export this value under a different name, or rename the declaration so it does not expose then."


*   Interface details: NO INTERFACES NEEDED

The changes required are modifications to diagnostic message strings within existing lint rule implementations. No new public functions, classes, or methods need to be created. The tests verify the exact text of diagnostic output produced by the following existing rules:

- `lint/suspicious/noShorthandPropertyOverrides` — implementation in `crates/biome_css_analyze/src/lint/suspicious/no_shorthand_property_overrides.rs`
- `lint/nursery/noRootType` — implementation in `crates/biome_graphql_analyze/src/lint/nursery/no_root_type.rs`
- `lint/a11y/noAutofocus` — implementation in `crates/biome_html_analyze/src/lint/a11y/no_autofocus.rs`
- `lint/suspicious/noSelfCompare` — implementation in `crates/biome_js_analyze/src/lint/suspicious/no_self_compare.rs`
- `lint/suspicious/noThenProperty` — implementation in `crates/biome_js_analyze/src/lint/suspicious/no_then_property.rs`

All required message text is documented in requirements.json.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.