Implement enhancements to the Go-based template language parser to support more advanced expression patterns and improve error handling. Extend the parser to handle slice and map indexing in template expressions, support multiline parameter lists and string expressions, and introduce a subsystem for extracting Go expressions from control-flow statements.

*   Enhance the `TemplElementExpression` parser (in `parser/v2`) to:
    *   Support slice indexing with integer indices (e.g., `@templates[0]()`) and map indexing with string keys (e.g., `@templates["key"]()`).
    *   Allow method calls on slice/indexed struct results (e.g., `@templates[0].CreateTemplate()`).
*   Extend the template parser (in `parser/v2`) to:
    *   Support multiline parameter lists in template definitions.
    *   Support multiline string expressions within templates.
*   Implement functions in `parser/v2/goexpression/parse.go` for expression extraction:
    *   `If(content string) (start, end int, err error)`: Extracts condition expressions from strings starting with "if ".
    *   `For(content string) (start, end int, err error)`: Extracts loop expressions from strings starting with "for ".
    *   `Switch(content string) (start, end int, err error)`: Extracts switch expressions from strings starting with "switch ".
    *   `Case(content string) (start, end int, err error)`: Extracts case/default clauses from strings starting with "case" or "default:".
    *   `Expression(content string) (start, end int, err error)`: Extracts general Go expressions, including package-qualified calls, slice/map index calls, and spread expressions.
    *   `SliceArgs(content string) (string, error)`: Extracts argument list content from a string, handling various argument formats and returning an error on failure.
*   Ensure parse errors for unclosed statements (e.g., DOCTYPE, HTML comments, Go multi-line comments, and unterminated control-flow expressions) report error positions as Index: 0, Line: 0, Col: 0.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.