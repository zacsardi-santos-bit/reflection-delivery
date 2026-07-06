Implement the `isClaudePreviewLaunch` and `resolveDevCommandOptions` functions to integrate Storybook's dev server with AI coding assistants, ensuring correct port assignment and browser behavior based on the environment context.

*   Implement `isClaudePreviewLaunch` in `code/core/src/shared/utils/agent-environment.ts`:
    *   Accept an `env` object (key-value string map).
    *   Return `true` if `CLAUDE_AGENT_SDK_VERSION` is present and `AI_AGENT` is absent.
    *   Return `false` if both `CLAUDE_AGENT_SDK_VERSION` and `AI_AGENT` are present, or if `CLAUDE_AGENT_SDK_VERSION` is absent.

*   Implement `resolveDevCommandOptions` in `code/core/src/bin/dev-options.ts`:
    *   Accept `cliOptions` and an optional `options` object containing an `env` object.
    *   Apply environment variable overrides to CLI options:
        *   `CI` overrides `ci`.
        *   `SBCONFIG_CONFIG_DIR` overrides `configDir`.
        *   `SBCONFIG_HOSTNAME` overrides `host`.
        *   `SBCONFIG_STATIC_DIR` overrides `staticDir`.
    *   Ensure truthy environment values take precedence over CLI-provided values.
    *   Resolve port based on context:
        *   Outside Claude preview mode: `--port` CLI option > `SBCONFIG_PORT` env var > `PORT` env var.
        *   In Claude preview mode: `PORT` env var > `--port` CLI option > `SBCONFIG_PORT` env var.
    *   Force `open` option to `false` in Claude preview mode, regardless of CLI value.
    *   Preserve `open` option value outside Claude preview mode.
    *   Preserve all unrelated CLI options (e.g., `https`, `smokeTest`).
    *   Validate port values:
        *   Ensure they are integers between 1 and 65535.
        *   Trigger error for invalid values with message: 'Port must be a valid number from 1 to 65535, received "<value>".' for strings, and without quotes for numbers.
        *   Include 'at port' in the error message.
        *   Treat empty or whitespace-only strings as invalid.
        *   Do not interpolate placeholder strings like `$PORT`; treat them as invalid.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.