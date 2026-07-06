## Description

When Storybook's "create story file" feature generates new story files, it embeds the component's file path directly into the generated TypeScript/JavaScript source code. If the component file path contains special characters — such as quotes, backticks, dollar signs, or backslashes — those characters are written verbatim into the generated code, which can break the syntax of the output or allow code injection.

For example, a component file path containing a single quote character could cause the generated import statement to close the string prematurely, producing a syntax error or injecting arbitrary code into the generated file.

## Expected Behavior

- A utility function should be introduced to escape special characters in strings before they are inserted into generated code templates.
- Characters that must be escaped include: backslashes, single quotes, double quotes, backticks, and dollar signs.
- Backslashes must be escaped first (before other characters) to avoid double-escaping.
- Normal file paths without special characters should pass through unchanged.
- The function should handle any combination of multiple special characters correctly.

## Why This Matters

Without this escaping, any component file path with unusual characters (whether by accident or intent) can produce broken or dangerous generated story files. This is a correctness and security issue for users who have component files with non-standard naming.
