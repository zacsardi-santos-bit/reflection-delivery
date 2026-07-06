## Description

The HTML formatter crashes with a panic when trying to format HTML files that contain inline elements placed directly before self-closing void elements. This is a very common HTML pattern — for example, wrapping text in a semantic element and then following it with a line break or image — and it should be handled gracefully by the formatter.

## Expected Behavior

- When an inline element's closing tag is immediately followed by a self-closing void element (like a line break or an image), the formatter should produce output identical to the input.
- The formatter should not panic or raise any internal error about tokens not being processed.
- Multiple variants of this pattern should work correctly: different kinds of inline elements followed by different kinds of self-closing elements should all format without errors and without altering the structure or whitespace of the input.

## Why This Matters

This crash makes the formatter unusable on a large category of valid, real-world HTML. Developers writing documentation, web pages, or component templates often use inline elements followed immediately by self-closing elements. The formatter should handle these patterns stably and preserve the existing layout rather than crashing or altering the content.
