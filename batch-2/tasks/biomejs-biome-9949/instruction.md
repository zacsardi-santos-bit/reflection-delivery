I'd like to add a new linting rule to Biome that flags embedded frame elements without a proper sandbox restriction.

*   Must implement a lint rule named `useIframeSandbox` in the `nursery` group under the path `lint/nursery/useIframeSandbox`, applicable to both HTML files and JSX/TSX files.

*   In HTML, the rule must flag any `<iframe>` element that has no `sandbox` attribute at all. Elements with a boolean `sandbox` attribute (no value), `sandbox=""`, or `sandbox="<any value>"` must NOT be flagged.

*   In JSX/TSX, the rule must flag `<iframe />` elements with no sandbox attribute AND `<iframe sandbox />` elements where the sandbox attribute is a boolean (has no string value assigned). Elements with `sandbox=""` or `sandbox="<any value>"` must NOT be flagged.

*   In JSX/TSX, an `<iframe>` element that uses spread props (e.g., `{...props}`) must NOT be flagged, even if no explicit `sandbox` attribute appears, because the sandbox may be supplied via the spread.

*   Non-iframe elements (such as `<a>`, `<span>`, `<button>`) must never trigger this rule in either HTML or JSX contexts.

*   The rule must emit an error-level diagnostic with the message: "Iframe doesn't have the sandbox attribute."

*   The diagnostic must include an informational note: "The sandbox attribute enables an extra set of restrictions for the content in the iframe, protecting against malicious scripts and other security threats."

*   The diagnostic must include an informational suggestion note: "Provide a sandbox attribute when using iframe elements."


*   Interface details: Type: Rule (Lint)
Name: useIframeSandbox
Location: crates/biome_html_analyze/src/lint/nursery/use_iframe_sandbox.rs
Description: HTML lint rule that flags <iframe> elements missing the sandbox attribute. Must be declared in the nursery group and registered so it is picked up by the HTML analyzer.

Type: Rule (Lint)
Name: useIframeSandbox
Location: crates/biome_js_analyze/src/lint/nursery/use_iframe_sandbox.rs
Description: JSX/TSX lint rule that flags <iframe> elements without a sandbox string-value attribute. Must be declared in the nursery group and registered so it is picked up by the JS/JSX analyzer.

Notes on expected diagnostic output:
- Rule category path: lint/nursery/useIframeSandbox
- Error message: "Iframe doesn't have the sandbox attribute."
- Informational note 1: "The sandbox attribute enables an extra set of restrictions for the content in the iframe, protecting against malicious scripts and other security threats."
- Informational note 2 (suggestion): "Provide a sandbox attribute when using iframe elements."
- Diagnostic span covers the entire iframe element (opening tag through closing tag or self-close).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.