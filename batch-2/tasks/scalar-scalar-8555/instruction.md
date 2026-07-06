I'm working on our API documentation viewer and I've noticed that when an API description uses the underline-style headings — where you write the heading text on one line and then put a row of equal signs or dashes underneath it — those headings never show up in the navigation sidebar.

*   The getMarkdownHeadings function must accept a markdown string and return an array of heading objects, each with a numeric `depth` field (1–6) and a string `value` field containing the plain-text heading content.

*   The getMarkdownHeadings function must recognize ATX-style headings: lines starting with 1 to 6 hash characters followed by a space. Lines with 7 or more hash characters must not be recognized as headings. A hash character that does not appear at the very beginning of a line must not be treated as a heading. A hash character not followed by a space (e.g., '#NoSpace') must not be treated as a heading.

*   The getMarkdownHeadings function must recognize setext-style headings: a line of text followed immediately by a line composed entirely of '=' characters is a depth-1 heading; followed by a line of '-' characters is a depth-2 heading.

*   The getMarkdownHeadings function must correctly parse input with Windows line endings (CRLF).

*   The getMarkdownHeadings function must support closing ATX headings (e.g., '## Example Heading ##') by stripping the trailing hash characters from the value.

*   The getMarkdownHeadings function must strip all inline markdown formatting from the heading value: bold markers, italic markers, inline code backticks, strikethrough markers, link syntax (keeping link text), image syntax (keeping alt text), and HTML tags.

*   The getMarkdownHeadings function must ignore any content that appears inside fenced code blocks (opened with triple backticks or triple tildes, with or without a language identifier) and inside indented code blocks (4-space indent). Headings appearing on those lines must not be included in the output.

*   The getMarkdownHeadings function must return an empty array when the input is an empty string or when the input contains no headings.

*   The description navigation builder (traverseDescription) must correctly recognize setext-style headings in API description text and include them as navigation entries with proper nesting, titles, and type fields, in the same way it handles ATX-style headings.


*   Interface details: Type: Function
Name: getMarkdownHeadings
Location: packages/helpers/src/markdown/get-markdown-headings.ts
Signature: getMarkdownHeadings(markdown: string) -> Array<{ depth: number; value: string }>
Description: Parses a markdown string and returns an array of heading objects. Each object has a `depth` field (number 1–6) indicating the heading level and a `value` field (string) containing the plain-text content of the heading with all inline formatting stripped. Must be exported as a named export. This is a new file that must be created.

Notes on required behavior:
- ATX headings: lines starting with 1–6 `#` characters followed by a space are recognized as headings. Lines with 7 or more `#` characters are NOT headings. A hash that does not appear at the start of a line is not a heading. A `#` not followed by a space (e.g., `#NoSpace`) is not a heading.
- Setext headings: a line of text followed by a line of `=` characters is a depth-1 heading; followed by a line of `-` characters is a depth-2 heading.
- Closing ATX headings: trailing `##` at the end of an ATX heading line is stripped.
- Windows line endings (CRLF) must be handled correctly.
- Content inside fenced code blocks (opened with triple backticks or triple tildes, with or without a language identifier) is NOT treated as headings.
- Content inside indented code blocks (4-space indent) is NOT treated as headings.
- Inline formatting stripped from `value`: bold (`**text**`), italic (`*text*`), inline code (`` `code` ``), strikethrough (`~~text~~`), link text (`[text](url)` → `text`), image alt text (`![alt](url)` → `alt`), and HTML tags.
- Returns an empty array for empty input or input with no headings.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.