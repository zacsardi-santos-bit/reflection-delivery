I'm working on the HTML accessibility linter and need to add a new lint rule that validates whether anchor elements are used correctly.

*   The useValidAnchor lint rule must apply to anchor elements (the lowercase 'a' tag) in HTML, Astro, Svelte, and Vue template files; elements with a different tag name (such as a 'Link' component) must not be flagged.

*   When an anchor element has an href attribute whose value is a javascript: URL (e.g., 'javascript:void(0)'), the rule must emit a diagnostic highlighting the href attribute with the error message 'Provide a valid value for the attribute href.' and the informational note 'The href attribute should be a valid URL'.

*   When an anchor element has a bare href attribute with no value (boolean attribute form), the rule must emit a diagnostic highlighting the href attribute with the error message 'Provide a valid value for the attribute href.' and the informational note 'The href attribute should be a valid URL'.

*   When an anchor element has an onclick event handler attribute (in HTML, Astro, or Svelte files) but no valid href, the rule must emit a diagnostic highlighting the onclick attribute with the error message 'Use a button element instead of an a element.' and the informational note 'Anchor elements should only be used for default sections or page navigation'.

*   When an anchor element has no href attribute at all, the rule must emit a diagnostic highlighting the opening element tag with the error message 'Provide a href attribute for the a element.' and the informational note 'An anchor element should always have a href'.

*   In Vue files, an anchor element that has a Vue-style click event directive (e.g., '@click') but no href attribute must trigger the 'Provide a href attribute for the a element.' diagnostic (not the 'Use a button element instead' diagnostic), because the Vue event directive is not treated as an equivalent to an onclick handler for this rule.

*   Anchor elements with a valid href attribute value — including fragment URLs (e.g., '#id'), absolute URLs (e.g., 'https://example.com'), and dynamic expression bindings (e.g., href={expression} in Astro/Svelte or :href in Vue) — must not trigger any diagnostic.

*   The rule must be registered under the rule identifier 'lint/a11y/useValidAnchor' and produce diagnostics in the expected snapshot format when run against the spec test files.


*   Interface details: Type: LintRule
Name: useValidAnchor
Location: crates/biome_html_analyze/src/lint/a11y/use_valid_anchor.rs
Description: A new accessibility lint rule that validates anchor elements in HTML-like files (HTML, Astro, Svelte, Vue). The rule must be registered under the rule identifier "lint/a11y/useValidAnchor".

The rule must produce exactly three distinct diagnostics:

1. **Invalid href value** — triggered when an anchor element has an href attribute with a javascript: URL or a bare (valueless) href attribute.
   - Error message: "Provide a valid value for the attribute href."
   - Informational note: "The href attribute should be a valid URL"
   - Diagnostic span: the href attribute node

2. **Onclick without href** — triggered when an anchor element has an onclick event handler attribute (standard HTML attribute name "onclick") but no valid href. Applies in HTML, Astro, and Svelte files only.
   - Error message: "Use a button element instead of an a element."
   - Informational note: "Anchor elements should only be used for default sections or page navigation"
   - Diagnostic span: the onclick attribute node

3. **Missing href** — triggered when an anchor element has no href attribute at all.
   - Error message: "Provide a href attribute for the a element."
   - Informational note: "An anchor element should always have a href"
   - Diagnostic span: the opening element tag

The rule must be placed in the spec test directory at:
  crates/biome_html_analyze/tests/specs/a11y/useValidAnchor/

The rule applies only to lowercase "a" elements; named component elements (e.g., "Link") must not be flagged. Dynamic href expressions (e.g., `href={somewhere}` in Astro/Svelte, `:href="..."` Vue bindings) are considered valid. Vue-style event directives (e.g., `@click`) are not recognized as onclick-equivalent handlers and do not trigger the "use button" diagnostic — instead the anchor generates the "Provide a href attribute" diagnostic.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.