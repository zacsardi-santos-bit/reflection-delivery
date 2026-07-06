I'd like to be able to add comments directly inside HTML element attribute lists in my templ templates.

*   The AttributeComment struct must be defined in the parser/v2 package with exactly three fields: Comment (string, the text content of the comment), Multiline (bool, true for block-style comments and false for single-line comments), and Range (Range, position information with From and To positions). It must implement the Attribute interface.

*   The element attribute parser must recognize single-line comment syntax (// ...) within attribute lists and produce an AttributeComment node with Multiline set to false and Comment set to all text following the // marker up to the end of the line.

*   The element attribute parser must recognize block comment syntax (/* ... */) within attribute lists and produce an AttributeComment node with Multiline set to true and Comment set to all text between the /* and */ delimiters.

*   When an element's attribute list contains a single-line comment (which introduces a newline), the parsed Element's IndentAttrs field must be set to true.

*   AttributeComment nodes must be positioned correctly in the Attributes slice of the parsed Element, interleaved with other attribute types in source order.

*   The code generator must handle AttributeComment nodes encountered during attribute code generation by skipping them entirely — no HTML or other output should be emitted for attribute comments.

*   A component function named TestAttributeComments must exist in package testattributecomments at generator/test-attribute-comments/ and must return a templ.Component. An expected.html file must exist in the same directory containing the exact HTML that the component renders at runtime.


*   Interface details: Type: Struct
Name: AttributeComment
Location: parser/v2/types.go
Description: Represents a comment (either single-line or multi-line) that appears within the attribute list of an HTML element. Must implement the Attribute interface (requires String(), Write(w io.Writer, indent int) error, Visit(v Visitor) error, and Copy() Attribute methods).
Fields:
  Comment   string   // Text content of the comment (everything after "//" or between "/*" and "*/")
  Multiline bool     // true for /* */ block comments, false for // single-line comments
  Range     Range    // Source position range with From and To positions (each with Index, Line, Col int fields)

Type: Function
Name: TestAttributeComments
Location: generator/test-attribute-comments/template_templ.go (or generated from generator/test-attribute-comments/template.templ)
Signature: TestAttributeComments() templ.Component
Description: A templ component function in package testattributecomments that demonstrates the use of comments within element attribute lists. The directory must also contain an expected.html file whose content matches the HTML output rendered by this component.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.