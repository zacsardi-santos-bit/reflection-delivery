Implement a robust HTML tokenizer in `lib/html/walkHtmlTokens.js` that correctly processes various HTML attribute styles and invokes callbacks for different token types. Ensure the tokenizer supports boolean attributes, empty-string attributes, and unquoted attribute values, while maintaining the ability to reconstruct the original input string from the token slices.

Requirements:

*   Implement a function `walkHtmlTokens(input, start, handlers)` in `lib/html/walkHtmlTokens.js`.
    *   Accepts an HTML string `input`, a starting position `start`, and a `handlers` object with optional callback methods.
*   Export three numeric constants on `walkHtmlTokens`:
    *   `QUOTE_NONE = 0` for unquoted attribute values.
    *   `QUOTE_DOUBLE = 1` for double-quoted attribute values.
    *   `QUOTE_SINGLE = 2` for single-quoted attribute values.
*   Callback methods on `handlers` object:
    *   `openTag(input, start, end, nameStart, nameEnd, selfClosing) -> number`
        *   Called for each open tag, with `start/end` spanning the complete tag.
        *   `nameStart/nameEnd` identify the tag name.
        *   `selfClosing` is true if the tag ends with '/>', false otherwise.
        *   Must return the new parse position.
    *   `closeTag(input, start, end, nameStart, nameEnd) -> number`
        *   Called for each close tag, with `start/end` spanning the complete tag.
        *   `nameStart/nameEnd` identify the tag name.
        *   Must return the new parse position.
    *   `attribute(input, nameStart, nameEnd, valueStart, valueEnd, quoteType) -> number`
        *   Called before `openTag` for each attribute.
        *   `nameStart/nameEnd` identify the attribute name.
        *   `valueStart = -1` for boolean attributes; otherwise, `valueStart/valueEnd` span the value content.
        *   `quoteType` is one of `QUOTE_NONE`, `QUOTE_DOUBLE`, or `QUOTE_SINGLE`.
        *   Return `nameEnd` for boolean attributes, `valueEnd + 1` for quoted values, or `valueEnd` for unquoted values.
    *   `comment(input, start, end) -> number`
        *   Called for each HTML comment, with `start/end` spanning the complete comment.
        *   Must return the new parse position.
    *   `text(input, start, end) -> number`
        *   Called for text content between tags, with `start/end` spanning the text.
        *   Must return the new parse position.
*   Ensure the roundtrip property: concatenating slices from `openTag`, `closeTag`, `comment`, and `text` handlers must reconstruct the original input.
*   Handle edge cases:
    *   No callbacks for empty input strings.
    *   Single text callback for input with no HTML tags.
    *   Treat a lone '<' at the end of input as text.
    *   Correctly identify self-closing tags and boolean attributes.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.