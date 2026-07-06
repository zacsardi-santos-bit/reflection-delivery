## Description

The table rendering component in the CLI displays markdown-containing cell content as raw text, meaning users see raw formatting syntax characters instead of properly styled output. For example, a cell containing bold or italic text shows the surrounding asterisks or underscores literally rather than rendering the text visually styled. Additionally, column widths are calculated using the raw string length (including markdown syntax), causing layout issues where columns are wider or narrower than needed for the actual visible content.

Beyond the table renderer, there is no standalone utility available to non-component parts of the codebase for converting inline markdown text to terminal-compatible styled output with ANSI escape codes. This makes it impossible to produce properly styled terminal output outside of the component-based rendering system.

## Expected Behavior

- Markdown syntax in table cells should be rendered as styled text: bold, italic, strikethrough, inline code, links, and underlines should all display without raw markers.
- Table column widths should be calculated based on the visual text length after markdown is stripped, not the raw markdown string length.
- A utility function should be available that converts markdown text (including bold, italic, bold-italic, strikethrough, code, links, bare URLs, and underlined text) to a string with ANSI escape codes applied, accepting an optional default color parameter.
- The utility should support custom default colors (both named colors and hex values), while inline code always uses the accent color and link URLs always use the link color, regardless of the default color override.
- Markdown syntax inside inline code spans must not be parsed.

## Why This Matters

Users viewing tables with markdown-formatted content see ugly raw syntax characters instead of clean formatted output. The column layout can also be broken because column width is computed from raw text that includes markdown markers. Having a standalone, non-component path for rendering inline markdown enables consistent styled output across all parts of the CLI.
