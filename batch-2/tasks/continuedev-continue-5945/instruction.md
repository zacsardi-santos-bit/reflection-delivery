Implement a centralized utility module to manage rule file creation and configuration in a consistent manner. Extract and consolidate logic for sanitizing rule names, constructing file paths, and generating markdown with YAML frontmatter. Update existing tools to use these shared utilities.

*   Implement `sanitizeRuleName` in `core/config/markdown/createMarkdownRule.ts`:
    *   Convert names to lowercase.
    *   Remove non-alphanumeric characters except spaces and hyphens.
    *   Replace multiple spaces with a single hyphen.
    *   Trim leading/trailing dashes.
    *   Return an empty string for empty or whitespace-only inputs.

*   Implement `createRuleFilePath` in `core/config/markdown/createMarkdownRule.ts`:
    *   Accept `workspaceDir` and `ruleName`.
    *   Use `sanitizeRuleName` to sanitize `ruleName`.
    *   Return path: `{workspaceDir}/.continue/rules/{sanitized-name}.md`.

*   Implement `createMarkdownWithFrontmatter` in `core/config/markdown/createMarkdownRule.ts`:
    *   Format: `---\n{yaml-frontmatter}\n---\n\n{markdown}`.
    *   For empty frontmatter, produce: `---\n{}\n---\n\n{markdown}`.
    *   Ensure output is parseable by `parseMarkdownRule`.

*   Implement `createRuleMarkdown` in `core/config/markdown/createMarkdownRule.ts`:
    *   Accept `name`, `ruleContent`, and optional `options` (description, globs, alwaysApply).
    *   Format markdown: `# {name}\n\n{ruleContent}`.
    *   Include frontmatter fields only if provided; alwaysApply only if explicitly false.

*   Export `RULE_FILE_EXTENSION` with value `'md'` from `core/config/markdown/createMarkdownRule.ts` and re-export via `core/config/markdown/index.ts`.

*   Update `core/config/markdown/index.ts`:
    *   Re-export all from `createMarkdownRule`.
    *   Re-export `parseMarkdownRule`.

*   Implement `getFileContent` in `core/config/workspace/workspaceBlocks.ts`:
    *   For "rules", return markdown with "# New Rule", "Your rule content", "A description of your rule".
    *   For other block types, return YAML with specified content for each type.

*   Implement `findAvailableFilename` in `core/config/workspace/workspaceBlocks.ts`:
    *   Map block types to base filenames.
    *   Use `RULE_FILE_EXTENSION` for "rules", "yaml" for others.
    *   Append numeric suffixes to find available filenames.

*   Update `createRuleBlockImpl` in `core/tools/implementations/createRuleBlock.ts`:
    *   Use `createRuleFilePath` and `createRuleMarkdown`.
    *   Exclude alwaysApply from frontmatter.
    *   Use `ide.getWorkspaceDirs()` for file path construction.
    *   Call `ide.writeFile()` and `ide.openFile()` with the path.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.