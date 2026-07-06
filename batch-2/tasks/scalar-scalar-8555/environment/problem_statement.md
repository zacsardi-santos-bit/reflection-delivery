## Description

Our API documentation viewer builds a navigation tree from the description text of an API spec. Currently, when an API description uses underline-style headings (where a line of text is followed by a row of equal signs or dashes to indicate heading level), those headings are silently ignored and never appear in the navigation sidebar. Authors who prefer this heading style get no navigation entries for their sections.

Additionally, heading extraction logic is duplicated across the codebase. We need a single, well-tested utility in the helpers package that extracts headings from markdown text, so different parts of the app can rely on one consistent implementation.

## Expected Behavior

- A new, standalone heading extraction utility should be created in the helpers package. It should accept a markdown string and return a list of heading objects, each with a heading level (1–6) and the plain-text heading content.
- The utility must support both ATX-style headings (prefixed with hash characters) and setext-style headings (underlined with equal signs or dashes).
- Inline formatting (bold, italic, inline code, strikethrough, links, images, and HTML tags) should be stripped from the returned heading text.
- Headings appearing inside fenced or indented code blocks must be ignored.
- The utility must handle edge cases: Windows line endings, closing hash sequences, headings with 7+ hash characters (invalid), and hashes not at the start of a line.
- The description navigation builder must be updated to use this utility so that setext-style headings are properly included in the navigation tree.

## Why This Matters

API authors who use underline-style headings in their descriptions currently see no navigation entries for those sections, making it impossible to navigate long descriptions. Centralizing heading extraction into a shared utility also removes duplication and ensures consistent behavior across the application.
