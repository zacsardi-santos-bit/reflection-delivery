## Description

The rule file creation system has scattered logic that makes it difficult to maintain and reuse. Currently, the code for sanitizing rule names, constructing rule file paths, and generating formatted markdown with YAML frontmatter is duplicated or missing from shared utilities. There is also no standardized way to generate template content for different types of configuration blocks, and no utility to find an available filename when creating new workspace configuration files to avoid overwriting existing ones.

## Expected Behavior

- A shared utility should exist for sanitizing rule names into safe filenames (lowercase, hyphens instead of spaces, no special characters)
- A shared utility should construct the correct rule file path within the workspace's rules configuration directory from a workspace directory and rule name
- A shared utility should create markdown content with properly formatted YAML frontmatter that can be parsed back correctly
- A shared utility should create complete rule markdown files from a name, content body, and optional metadata (description, file patterns, and whether to always apply)
- The workspace block file creation system should generate appropriate template content based on block type — markdown format for rules, YAML format for other types
- When creating a new configuration file, the system should find an available filename by incrementing a counter suffix if the base name already exists
- The rule creation tool should use these shared path and content utilities instead of duplicating the logic

## Why This Matters

Centralizing these utilities prevents logic drift between different parts of the codebase that need to create or manage rule files. It also ensures that rule files always use a consistent format and path structure, regardless of which part of the system creates them.
