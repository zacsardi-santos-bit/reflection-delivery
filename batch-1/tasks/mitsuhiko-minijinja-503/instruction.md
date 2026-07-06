Implement support for line-oriented template syntax in the Rust template engine. Add configuration options for a line statement prefix and a line comment prefix to enhance the template's readability and maintainability.

*   Update `SyntaxConfigBuilder` in `minijinja/src/syntax.rs` to include:
    *   `line_statement_prefix` method:
        *   Signature: `pub fn line_statement_prefix<S>(&mut self, s: S) -> &mut Self where S: Into<Cow<'static, str>>`
        *   Ensure it is gated on the `custom_syntax` feature.
        *   Accepts a value convertible to `Cow<'static, str>`.
        *   Configures a prefix that, when it appears at the start of a line (preceded only by spaces or tabs), is treated as a block control statement.
        *   Returns `&mut Self` for method chaining.
    *   `line_comment_prefix` method:
        *   Signature: `pub fn line_comment_prefix<S>(&mut self, s: S) -> &mut Self where S: Into<Cow<'static, str>>`
        *   Ensure it is gated on the `custom_syntax` feature.
        *   Accepts a value convertible to `Cow<'static, str>`.
        *   Configures a prefix that, when it appears anywhere on a line, causes everything from the prefix to the end of the line (including the newline) to be stripped.
        *   Returns `&mut Self` for method chaining.

*   Ensure the lexer processes templates with the following behaviors:
    *   Emit a `BlockStart` token when a line starts with the configured `line_statement_prefix`, followed by parsed expression tokens, and a `BlockEnd` token.
    *   Do not recognize the `line_statement_prefix` as a line statement if it appears after non-whitespace content on a line.
    *   Support multi-line expressions for line statements when parentheses or brackets are unbalanced.
    *   Strip content from the `line_comment_prefix` to the end of the line, including the newline, from the token stream.
    *   Remove lines entirely if they consist solely of the `line_comment_prefix` and trailing text.

*   Allow simultaneous configuration of both `line_statement_prefix` and `line_comment_prefix` through `SyntaxConfig::builder()`, ensuring they work together in a single template.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.