Implement a decoding utility within the HTML tokenizer module to convert HTML-encoded strings back to their actual character values. Ensure that the tokenizer correctly processes these encodings without misinterpreting them as HTML syntax.

*   Update the `walkHtmlTokens` function to:
    *   Correctly tokenize HTML containing named character references (e.g., `&amp;`, `&lt;`, `&gt;`) in text nodes and attribute values.
    *   Correctly tokenize HTML containing decimal numeric character references (e.g., `&#65;`) and hexadecimal numeric character references (e.g., `&#x41;`, `&#X41;`).
    *   Handle unknown named references, malformed/empty numeric references, and bare ampersands without breaking tokenization.

*   Implement the `decodeHtmlEntities` function with the following requirements:
    *   Export it as a property on the `walkHtmlTokens` function object: `walkHtmlTokens.decodeHtmlEntities = ...`.
    *   Accept a raw string and return the string with HTML character references decoded.
    *   Decode the following named entities:
        *   `&amp;` to `&`
        *   `&lt;` to `<`
        *   `&gt;` to `>`
        *   `&quot;` to `"`
        *   `&apos;` to `'`
        *   `&nbsp;` to the non-breaking space character (U+00A0).
    *   Decode decimal numeric character references (e.g., `&#65;` to `A`).
    *   Decode hexadecimal numeric character references with both lowercase and uppercase x (e.g., `&#x41;` and `&#X41;` to `A`).
    *   Leave unknown named entities, malformed/empty numeric references, and bare ampersands unchanged (e.g., `&unknown;`, `&#;`, `&#x;`, `bare & alone`).
    *   Correctly handle strings mixing plain text with decodable entities (e.g., `'foo &amp; bar &#x41; baz'` to `'foo & bar A baz'`).

*   Ensure that numeric character references in `srcset` attribute values remain encoded and are not decoded before being passed to the `srcset` parser.

*   When processing inline style tags through the CSS pipeline, ensure the compiled HTML output includes exactly one CSS source-comment data URL header across all CSS-typed style blocks.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.