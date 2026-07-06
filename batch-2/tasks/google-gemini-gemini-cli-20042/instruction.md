Implement a utility function to convert markdown text into styled terminal output using ANSI escape codes, and update the table renderer to display markdown-formatted content correctly. Ensure that column widths are calculated based on the visual text length after markdown is stripped.

*   Implement the `parseMarkdownToANSI` function in `packages/cli/src/ui/utils/InlineMarkdownRenderer.tsx` with the signature:
    *   `parseMarkdownToANSI(text: string, defaultColor?: string): string`
*   Ensure `parseMarkdownToANSI` handles various markdown syntaxes:
    *   Return plain text with no markdown syntax or URLs colorized with the default primary text color (`theme.text.primary`).
    *   Convert bold text (`**...**`) using `chalk.bold()`, with inner content recursively parsed.
    *   Convert italic text (`*...*` or `_..._`) using `chalk.italic()`.
    *   Convert bold-italic text (`***...***`) using both `chalk.bold()` and `chalk.italic()`.
    *   Convert strikethrough text (`~~...~~`) using `chalk.strikethrough()`.
    *   Convert inline code (`` `...` ``) using `theme.text.accent`, without parsing markdown inside.
    *   Convert markdown links (`[text](url)`) with link text in default color, URL in `theme.text.link` color.
    *   Convert bare URLs starting with `https?://` using `theme.text.link` color.
    *   Convert text with HTML underline tags (`<u>...</u>`) using `chalk.underline()`.
*   Support the `defaultColor` parameter to override the default primary text color for non-code and non-link elements, accepting named colors and hex values.
    *   Inline code must always use `theme.text.accent`.
    *   Link URLs must always use `theme.text.link`.
*   Ensure nested markdown formatting is supported recursively.
*   Update the `TableRenderer` component to:
    *   Render markdown in table cell content as visually formatted text, stripping markdown syntax markers.
    *   Calculate column widths based on the rendered text length, not the raw markdown string length.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.