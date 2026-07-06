Implement a feature to enhance file-writing and search tools by returning context-aware snippets of modified content and search results. Ensure that only relevant changes and their surrounding context are shown, reducing unnecessary content while preserving essential information.

*   Implement the `getDiffContextSnippet` function in `packages/core/src/tools/diff-utils.ts`:
    *   Accept `original` and `modified` strings, and an optional `contextLines` number (default to 5).
    *   Return a snippet with only changed lines and surrounding context.
    *   Return full modified content if `original` is empty or unchanged.
    *   Merge overlapping context windows; separate distant ones with '...'.
    *   Prefix and suffix with '...' if content is omitted before or after changes.
    *   Show surrounding lines for deletions, with '...' for omitted content.

*   Update the `WriteFileTool`:
    *   Ensure `execute` method returns `llmContent` starting with 'Here is the updated code:'.
    *   Include full content for new or small files.
    *   For large files, include changed lines and 5 lines of context, using '...' for omitted sections.

*   Enhance the `GrepTool`:
    *   Include context lines around matches when there are 3 or fewer matches.
    *   Format match lines as 'L{lineNumber}: {lineContent}' and context lines as 'L{lineNumber}- {lineContent}'.

*   Modify `RipGrepToolParams` in `packages/core/src/tools/ripGrep.ts`:
    *   Add an optional `context` field to control surrounding context lines.
    *   Default `context` value must be greater than 0, unless explicitly set to 0.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.