## Description

When documents are exported to HTML, the output relies on generic container elements with custom data attributes rather than proper semantic HTML. Screen readers and other assistive technologies cannot derive document structure from this output — headings are not real headings, lists are not real lists, quotes are not blockquotes, and so on.

There are also two specific bugs:

1. When the editor already emits an inner heading tag inside a heading block, the transformation creates a duplicate nested heading instead of replacing it cleanly. If the inner heading's level happens to differ from the block's declared level, both elements survive in the output, causing structural confusion.

2. When a heading block appears between two groups of list items within the same container, both groups end up merged into a single list instead of being split into two separate lists around the heading.

## Expected Behavior

- Heading blocks should produce a single semantic heading element at the correct level. If an inner heading already exists at the same level, it should not be duplicated. If the inner heading is at a different level, the block's declared level should win.
- If the document has no top-level heading after processing, one should be injected automatically using the document title, with an identifier that can be referenced by other elements.
- List items should be wrapped in the appropriate list element (unordered or ordered). A heading that appears between list items should cause them to be placed in separate lists, not merged.
- Quote blocks should become proper blockquote elements.
- Callout blocks should become aside elements announced as notes to assistive technology.
- Checklist items should be wrapped in an annotated list structure, and each checkbox should carry an indicator of its checked state readable by assistive technology.
- Code blocks should become pre/code elements, preserving language metadata and styling class names.
- Images without alternative text should receive an empty alt attribute; existing alt text must not be overwritten.
- The document body should be wrapped in a landmark region with a document role, linked to the document's title heading.

## Why This Matters

Exported HTML documents are used outside the editor, and users who rely on screen readers or keyboard navigation need proper semantic structure to understand and navigate the content. Without these fixes, the exported output is effectively opaque to assistive technologies.
