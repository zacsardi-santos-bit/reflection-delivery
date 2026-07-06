I'm working on a Markdown parser and I've noticed that headings and horizontal rules inside blockquotes are not being parsed correctly.

*   The `force_relex_at_line_start` method must be added to `BufferedLexer` in `crates/biome_parser/src/lexer.rs`. It must accept a lexer context parameter and return a syntax kind. It re-lexes the current token at the same source position while treating the position as if it were at the start of a line (i.e., as if `after_newline` / `after_line_break` is true), overriding whatever the lexer's current state says.

*   When a `BufferedLexer` wrapping a `MarkdownLexer` has consumed blockquote prefix tokens (`>` and a space) and is positioned at `---`, calling `force_relex_at_line_start(MarkdownLexContext::Regular)` must return `MD_THEMATIC_BREAK_LITERAL` (not `MINUS`). After the call, `current()` must also return `MD_THEMATIC_BREAK_LITERAL`.

*   The Markdown parser must correctly parse a setext heading inside a blockquote. Input `> Foo\n> ---\n` must produce HTML `<blockquote>\n<h2>Foo</h2>\n</blockquote>\n` and must be represented in the AST as `MdSetextHeader` with `underline_token: MD_SETEXT_UNDERLINE_LITERAL`.

*   The Markdown parser must correctly parse a setext heading with equals-sign underline inside a blockquote. Input `> Bar\n> ===\n` must produce an `MdSetextHeader` node with `underline_token: MD_SETEXT_UNDERLINE_LITERAL` (the `===` token).

*   Setext headings inside nested blockquotes must be parsed correctly. Input `> > Nested\n> > ---\n` must produce a nested `MdSetextHeader` with `underline_token: MD_SETEXT_UNDERLINE_LITERAL`.

*   A standalone thematic break inside a blockquote must be parsed as a thematic break, not a paragraph. Input `> ---\n` must produce HTML `<blockquote>\n<hr />\n</blockquote>\n`.

*   Spaced thematic break variants inside blockquotes must be parsed as `MdThematicBreakBlock` nodes: `> - - -\n`, `> * * *\n`, `> _ _ _\n`, and `> ___\n` must each produce a `MdThematicBreakBlock` after consuming the quote prefix.

*   Unspaced three-star sequences inside blockquotes (`> ***\n`) must NOT produce a thematic break; they must be parsed as a paragraph containing individual textual tokens.

*   Mixed-character sequences inside blockquotes (e.g. `> -*_\n`) must NOT be parsed as thematic breaks or setext heading underlines; they must remain as textual content within the preceding paragraph.

*   An indented code block inside a blockquote must terminate before a thematic break line. Input `>     code\n> ---\n` must produce HTML `<blockquote>\n<pre><code>code\n</code></pre>\n<hr />\n</blockquote>\n` — the code block ends and the `---` becomes a horizontal rule.


*   Interface details: Type: Method
Name: force_relex_at_line_start
Location: crates/biome_parser/src/lexer.rs
Signature: pub fn force_relex_at_line_start(&mut self, context: Lex::LexContext) -> Lex::Kind
Description: Method on `BufferedLexer<K, Lex>`. Re-lexes the current token in the given context, treating the current source position as if it were at the start of a line (sets `after_line_break` to `true`). This allows the parser to obtain line-start-gated tokens (such as thematic break literals) after consuming a blockquote prefix that left `after_line_break` as false. The method rewinds the lexer to the start of the current token position, sets `after_line_break: true`, re-lexes, and updates `current`. Returns the new syntax kind of the re-lexed token.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.