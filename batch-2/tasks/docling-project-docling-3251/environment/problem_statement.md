## Description

The markdown backend does not handle custom block-level tags for signatures and stamps that can appear in markdown documents. When a markdown file contains these semantically meaningful sections wrapped in custom tags, the conversion pipeline fails to recognize them and does not produce the expected structured output.

## Expected Behavior

- A markdown file containing a custom signature block should be correctly parsed during conversion, and when exported back to markdown, should render as a labeled heading ("Signature") followed by an image placeholder comment.
- Similarly, a custom stamp block should render as "Stamp" followed by the image placeholder comment in the exported markdown.
- The text content inside these blocks should be preserved in the internal document model as a child element of the corresponding classified picture element.
- The surrounding text content in the document should remain unaffected and appear in the correct order relative to these special elements.

## Why This Matters

Documents that originate from markdown sources and include embedded signature or stamp sections are increasingly common in workflows involving signed documents or certified copies. Without proper handling of these tags, the conversion pipeline either silently drops the content or produces malformed output, breaking downstream use of the converted document.
