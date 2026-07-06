## Description

Webpack's HTML loader fails to detect and rewrite asset source URLs for certain HTML elements, depending on how their attributes are written. Specifically, when an element has a boolean attribute (an attribute written without a value, such as a boolean flag on a form control), an empty-string attribute, or an attribute value that is not surrounded by double quotes, the loader skips over the asset reference entirely and leaves the original source path unchanged in the output.

## Expected Behavior

- An image or other asset element that also has a boolean attribute should have its source URL correctly rewritten to the content-hashed output path.
- An element with an empty-string attribute value should have its source URL correctly processed.
- An element with an unquoted attribute value should have its source URL correctly processed.
- The full variety of real-world HTML attribute styles should be handled without breaking asset detection.

## Additional Context

The root cause is that the underlying HTML token walker used by the loader does not properly support all attribute value styles. A robust, low-level HTML tokenizer should:
- Walk HTML content and fire callbacks for open tags, close tags, attributes, comments, and text nodes.
- Provide precise character-level position information in each callback so callers can slice the original string.
- Correctly identify boolean attributes, distinguish between double-quoted, single-quoted, and unquoted attribute values, and detect self-closing tags.
- Guarantee that concatenating all token slices exactly reconstructs the original HTML (lossless roundtrip).
- Gracefully handle edge cases such as empty input, plain text with no tags, and a lone less-than sign at the end of the input.

## Why This Matters

Users writing HTML with common shorthand patterns (boolean attributes for form controls, unquoted attribute values, etc.) currently end up with broken asset paths in their compiled output. The fix enables the HTML loader to handle all standard attribute styles correctly.
