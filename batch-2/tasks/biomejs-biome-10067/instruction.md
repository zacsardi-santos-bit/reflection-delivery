I'm working on improving the HTML accessibility linting in my project.

*   A new accessibility lint rule named 'useFocusableInteractive' must be implemented in the HTML analyzer's a11y lint category (crates/biome_html_analyze/src/lint/a11y/). The rule must be registered so it is discoverable by the spec test runner.

*   The rule must emit a diagnostic when an HTML element has an interactive ARIA role attribute (e.g. 'button', 'tab') but lacks a 'tabindex' attribute. The diagnostic must cover the element's opening tag span.

*   The primary diagnostic message must read: 'The HTML element with the interactive role "{role}" is not focusable.' where {role} is substituted with the actual role value.

*   The diagnostic must include an informational note: 'A non-interactive HTML element that is not focusable may not be reachable for users that rely on keyboard navigation, even with an added role like "{role}".' where {role} is substituted with the actual role value.

*   The diagnostic must include a second informational note or hint: 'Add a tabindex attribute to make this element focusable.'

*   The rule must NOT emit a diagnostic for an element that has an interactive ARIA role and also has a 'tabindex' attribute, regardless of the tabindex value (including '0' and '-1').

*   The rule must NOT emit a diagnostic for native interactive HTML elements (such as 'button') that are inherently focusable, even when they carry no explicit ARIA role.

*   The rule must NOT emit a diagnostic for elements whose ARIA role is non-interactive (e.g. 'h1', 'menu').


*   Interface details: Type: Rule (Rust struct)
Name: UseFocusableInteractive
Location: crates/biome_html_analyze/src/lint/a11y/use_focusable_interactive.rs
Description: Implements the HTML accessibility lint rule that detects elements with interactive ARIA roles that are missing keyboard focusability. Must be declared using the biome `declare_rule!` macro with the rule name "useFocusableInteractive", language "html", and category lint/a11y. The rule must be registered in crates/biome_html_analyze/src/lint/a11y/mod.rs so the spec test runner can discover it under the path lint/a11y/useFocusableInteractive.

Rule metadata:
- Rule name (as used in diagnostics and the spec directory): useFocusableInteractive
- Category path: lint/a11y/useFocusableInteractive
- Source file (Rust snake_case): use_focusable_interactive.rs
- Struct name (Rust PascalCase): UseFocusableInteractive

Diagnostic messages (exact strings required by snapshot tests):
- Primary error: `The HTML element with the interactive role "{role}" is not focusable.`
- Note 1: `A non-interactive HTML element that is not focusable may not be reachable for users that rely on keyboard navigation, even with an added role like "{role}".`
- Note 2 / Hint: `Add a tabindex attribute to make this element focusable.`

Where `{role}` is replaced by the actual ARIA role value found on the element (e.g. "button", "tab").

Spec test fixture directory: crates/biome_html_analyze/tests/specs/a11y/useFocusableInteractive/


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.