Enhance the folder trust dialog in the CLI to display detailed information about a project's configurations before users decide to trust it. Implement a scanning mechanism to identify and summarize the contents of a project's configuration directory, highlighting any potentially risky settings.

*   Implement the `FolderTrustDiscoveryService` class:
    *   Create a static method `discover(workspaceDir: string): Promise<FolderDiscoveryResults>` to scan a given workspace directory.
    *   Return a `FolderDiscoveryResults` object with arrays: `commands`, `mcps`, `hooks`, `skills`, `settings`, `securityWarnings`, and `discoveryErrors`.
    *   Ensure all result arrays are empty with no errors if the '.gemini' configuration directory is absent.
    *   Discover commands from `.toml` files in `{GEMINI_DIR}/commands/`, using filenames without extensions.
    *   Identify skills by subdirectory names containing a 'SKILL.md' file in `{GEMINI_DIR}/skills/`.
    *   Extract MCP server names from the 'mcpServers' key in `settings.json`.
    *   Extract hook command strings from the 'hooks' key in `settings.json`.
    *   Populate the `settings` array with top-level keys from `settings.json`, excluding 'mcpServers' and 'hooks'.
    *   Handle non-object values in `settings.json` by returning an empty `settings` array without errors.
    *   Capture malformed JSON errors with the message: 'Failed to discover settings: Unexpected token'.
    *   Generate security warnings for specific conditions with exact message strings provided.

*   Update the `FolderTrustDialog` component:
    *   Set the title to 'Do you trust the files in this folder?' and include a description about trusting folders.
    *   Accept an optional `discoveryResults` prop of type `FolderDiscoveryResults | null`.
    *   Display a 'This folder contains:' section with groups labeled for each category, showing item counts and names.
    *   Include 'Security Warnings:' and 'Discovery Errors:' sections when applicable.
    *   Strip ANSI escape codes from all displayed values.
    *   Handle terminal constraints by truncating content and prompting users to expand when necessary.
    *   Use scrolling instead of truncation in full-screen mode with the alternate buffer active.

*   Modify the `Scrollable` component:
    *   Set the default initial scroll position to the top (`scrollTop = 0`).
    *   Introduce a `scrollToBottom` prop to allow initial scrolling to the bottom when true.

*   Export `FolderTrustDiscoveryService` from `packages/core/src/index.ts` for access via '@google/gemini-cli-core'.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.