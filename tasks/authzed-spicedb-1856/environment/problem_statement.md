## Description

The SpiceDB schema language editor (LSP server) currently has no hover support. When a developer places their cursor over a type reference, relation name, or permission reference in a schema document, the editor returns nothing useful. It would be extremely helpful to see the source definition of the referenced element inline — similar to how most modern language servers work.

Additionally, when resolving references in the schema, the resolver returns positional information about where to find the target, but does not include the actual source code text of the target element or the position of the name within that text. This information is necessary for rendering rich hover previews.

There is also a parser bug where compound expressions (unions, intersections, exclusions, and arrow expressions) report an incorrect starting position — they claim to start somewhere inside the expression rather than at the beginning of their leftmost operand.

## Expected Behavior

- Hovering over any identifier in a schema document (type reference, relation, permission) should return the source code of the referenced definition, formatted as SpiceDB schema text.
- For definitions and caveats that have a body, the hover content should show a summarized form where the body is replaced with a comment placeholder rather than showing the full contents.
- The schema resolver should include two additional pieces of information with each resolved reference: the source code snippet of the target element, and an offset indicating where the name starts within that snippet.
- For caveat parameter references specifically, no target position is needed.
- Binary expression nodes in the parser AST should report their starting position as the start of their first (leftmost) operand.

## Why This Matters

Hover support is a fundamental LSP feature that improves developer experience when editing SpiceDB schemas. The corrected position information and hover metadata also enable downstream tooling (like editors and IDEs) to accurately display and navigate schema definitions.
