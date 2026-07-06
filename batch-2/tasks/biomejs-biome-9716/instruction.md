I'm working on adding an HTML-specific accessibility lint rule to biome that enforces meaningful, accessible content in heading elements.

*   The rule must be implemented as a lint rule named 'useHeadingContent' in the 'a11y' category within the biome_html_analyze crate, and must be registered so the spec test infrastructure can discover it.

*   The rule must apply to heading elements h1 through h6 in HTML, Vue, Svelte, and Astro files.

*   In plain HTML files, heading element name matching must be case-insensitive (e.g., H1 and h1 are both treated as heading elements). In Vue, Svelte, and Astro files, only lowercase heading element names (h1–h6) are checked; a heading element whose tag name starts with an uppercase letter is treated as a custom component and ignored.

*   A heading element must produce a diagnostic when it is empty (no children), contains only whitespace text, is self-closing (void-style with no content), or when the heading element itself has the aria-hidden attribute set to 'true'. When aria-hidden is present on the heading element itself, it takes priority over any accessible-name attributes such as aria-label.

*   A heading element must produce a diagnostic when all of its child elements have aria-hidden='true' and there is no other accessible text content.

*   A heading element must NOT produce a diagnostic when it has non-empty, non-whitespace text content; when it carries an aria-label attribute; when it carries an aria-labelledby attribute (HTML); when it carries a title attribute (HTML); when it has nested visible child elements; or when it contains mixed content (some aria-hidden children alongside visible text).

*   In HTML files, a self-closing img child element with a non-empty alt attribute is considered accessible content and must not cause a diagnostic. Img tag name matching for this purpose is case-insensitive in HTML (img, IMG, Img all match).

*   In Vue, Svelte, and Astro files, a child element whose tag name starts with an uppercase letter is treated as a custom component that may render accessible content at runtime, and must not cause a diagnostic — whether it is a paired element or self-closing.

*   When a diagnostic is emitted, the primary message must be exactly: 'Provide screen reader accessible content when using heading elements.' and the note must be exactly: 'All headings on a page should have content that is accessible to screen readers.'


*   Interface details: Type: Rule (Rust lint rule via biome_analyze framework)
Name: UseHeadingContent
Location: crates/biome_html_analyze/src/lint/a11y/use_heading_content.rs
Description: A lint rule that enforces heading elements (h1–h6) have content accessible to screen readers in HTML, Vue, Svelte, and Astro files. Must be declared using the `declare_lint_rule!` macro with `name: "useHeadingContent"`, `language: "html"`, and placed in the `a11y` group. The rule struct must be named `UseHeadingContent`.

Registration requirement: The rule module `use_heading_content` must be declared (i.e., `pub mod use_heading_content;`) in `crates/biome_html_analyze/src/lint/a11y/mod.rs`, and the rule must be registered in the biome_html_analyze rule registry so the spec test infrastructure can discover and execute it.

Diagnostic output (exact strings required by snapshot tests):
- Primary message: "Provide screen reader accessible content when using heading elements."
- Note: "All headings on a page should have content that is accessible to screen readers."
- Rule path shown in diagnostics: `lint/a11y/useHeadingContent`

Spec test directories (must exist and be recognized by the test runner):
- crates/biome_html_analyze/tests/specs/a11y/useHeadingContent/          (HTML tests)
- crates/biome_html_analyze/tests/specs/a11y/useHeadingContent/vue/       (Vue tests)
- crates/biome_html_analyze/tests/specs/a11y/useHeadingContent/svelte/    (Svelte tests)
- crates/biome_html_analyze/tests/specs/a11y/useHeadingContent/astro/     (Astro tests)


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.