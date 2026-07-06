## Description

The tool currently outputs selected markdown content in either markdown or JSON format. There is no way to get plain text output — a version of the content with all markdown syntax removed, leaving only the raw readable text. This makes it difficult to use the tool's output in downstream text-processing pipelines, since other tools often don't understand markdown formatting.

## Expected Behavior

- A new output format option should be available that strips all markdown formatting from the output
- In plain mode, inline styling (such as emphasis and bold) should be removed so that only the underlying text remains
- In plain mode, links should appear as only their visible display text, with no URL or reference notation
- In plain mode, code blocks should output just the code content, with no fencing or language labels; an empty code block should produce no output
- In plain mode, tables should render as rows of space-separated cell values, one row per line, with no alignment or separator rows
- Multiple consecutive blank lines should be collapsed to at most one blank line in the output
- The existing structured output format should also correctly serialize code blocks, with the code text, language, and block type all represented

## Why This Matters

Users who want to extract readable content from markdown documents and pass it to other tools (word counters, diff utilities, search tools, etc.) currently have no clean way to do so. A plain text mode completes the output format options and makes the tool much more composable in shell pipelines.
