Implement conversion utilities to translate MCP server configurations between JSON and YAML formats. Ensure the conversion handles different server types and provides warnings for unsupported fields. Facilitate the conversion of entire JSON config files to YAML blocks while collecting all warnings.

*   Implement `convertJsonMcpConfigToYamlMcpConfig`:
    *   Accept a server name (string) and a JSON-style MCP server config.
    *   Return an object with:
        *   `yamlConfig`: the converted YAML-style config.
        *   `warnings`: an array of strings describing any conversion issues.
    *   For STDIO JSON configs:
        *   Produce a YAML config with `name`, `type` set to 'stdio', and map `command`, `args`, and `env` directly.
    *   For SSE JSON configs:
        *   Produce a YAML config with `name` and `url`.
        *   Move `headers` to `requestOptions.headers`.
    *   For HTTP JSON configs:
        *   Set `type` to 'streamable-http'.
        *   Map `url` directly and move `headers` to `requestOptions.headers`.
    *   Omit `envFile` and add a warning: 'envFile is not supported'.
    *   Throw an error for invalid server configurations: 'Invalid MCP server configuration'.
    *   Convert `${VAR_NAME}` to `${{ secrets.VAR_NAME }}` in `env`.

*   Implement `convertYamlMcpConfigToJsonMcpConfig`:
    *   Accept a YAML-style MCP server config.
    *   Return an object with:
        *   `name`: server name.
        *   `jsonConfig`: the converted JSON-style config.
        *   `MCP_TIMEOUT`: string or undefined.
        *   `warnings`: an array of strings for unsupported fields.
    *   For STDIO YAML configs:
        *   Set `type` to 'stdio' and map `command`, `args`, and `env`.
        *   Set `MCP_TIMEOUT` from `connectionTimeout` or leave undefined.
        *   Omit `cwd` and `faviconUrl`, adding warnings for each.
    *   For SSE/HTTP YAML configs:
        *   Map `url` and promote `requestOptions.headers` to `headers`.
        *   Use `type` 'http' for 'streamable-http'.
    *   Omit unsupported `requestOptions` fields and add warnings.
    *   Throw an error for invalid server configurations: 'Invalid MCP server configuration'.
    *   Convert `${{ secrets.VAR_NAME }}` and `${{ inputs.VAR_NAME }}` to `${VAR_NAME}`.

*   Implement `converMcpServersJsonConfigFileToYamlBlocks`:
    *   Accept a `McpServersJsonConfigFile` object.
    *   Return an object with:
        *   `yamlConfigs`: an array of converted YAML configs.
        *   `warnings`: an array of all warnings from server conversions.
    *   Handle empty `mcpServers` map by producing empty arrays.
    *   Collect all warnings from all server conversions.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.