Implement hover support in the SpiceDB schema language server to display the source code definition of schema elements when hovered over in a document. Update the schema resolver to include source text and position information for referenced elements, and correct the parser's handling of compound expression positions.

*   Update `SchemaReference` struct in `pkg/development/resolver.go`:
    *   Add `TargetSourceCode` field to store the source code snippet of the referenced element.
    *   Add `TargetNamePositionOffset` field to indicate the rune offset of the element's name within `TargetSourceCode`.
    *   Ensure `TargetSourceCode` and `TargetNamePositionOffset` are set correctly for:
        *   Relation references: full source line with newline, offset 9.
        *   Permission references: full source line with newline, offset 11.
        *   Definition references with empty body: full text, offset 11.
        *   Definition references with non-empty body: summarized form, offset 11.
        *   Caveat references: summarized form with parameters, offset 7.
        *   Caveat parameter references: parameter annotation string, offset 0, no target position.

*   Modify the LSP server in `internal/lsp/handlers.go`:
    *   Store document entries as `trackedFile` structs with an unexported `contents` field.
    *   Ensure the `Get()` method returns `trackedFile` instead of a plain string.
    *   Implement `textDocHover` method to handle `textDocument/hover` requests:
        *   Resolve schema reference at cursor position.
        *   Return a `Hover` response with `Contents.Value` set to `TargetSourceCode` and `Contents.Language` set to "spicedb".

*   Update the `Hover` struct in `internal/lsp/lspdefs.go`:
    *   Ensure `Contents` field exposes `Language` and `Value` fields.

*   Advertise hover support in the server's capabilities on initialization.

*   Correct parser behavior in `pkg/schemadsl/parser`:
    *   Set start-rune of `NodeTypeUnionExpression`, `NodeTypeExclusionExpression`, `NodeTypeIntersectExpression`, and `NodeTypeArrowExpression` nodes to the start of their leftmost operand.

*   Add new test files in `pkg/schemadsl/parser/tests/`:
    *   `unionpos.zed` and `unionpos.zed.expected` to verify correct start positions in union expressions.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.