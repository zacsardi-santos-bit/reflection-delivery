## Description

Many AI development tools use a JSON-based format to configure MCP (Model Context Protocol) servers, while the internal project configuration uses a YAML-based format with a different structure and field naming conventions. There is currently no way to automatically convert between these two formats, forcing developers to manually rewrite their server configurations when importing from external tools or exporting to them.

## Expected Behavior

- Developers should be able to convert a JSON-style MCP server config entry into the equivalent YAML-style config, including proper handling of server types (process-based, SSE-based, and HTTP-based).
- Developers should be able to convert YAML-style MCP configs back into the JSON format used by external tools.
- An entire multi-server JSON config file should be convertible to an array of YAML configs in a single operation.
- When a field exists in one format but has no equivalent in the other, the conversion should succeed and produce a warning message describing what was dropped — rather than failing silently or throwing an error.
- Environment variable references use different syntax in each format. Conversion should automatically translate variable references in both directions so that values are preserved through round-trips.
- Conversion in both directions should throw a clear error for configs that cannot be matched to any known server type.

## Why This Matters

Developers frequently configure MCP servers in one tool and want to reuse that configuration in another. Without conversion utilities, they must manually adapt configurations, which is error-prone and time-consuming. Bidirectional conversion with clear warnings for unsupported fields enables safe import and export of server configurations across tooling boundaries.
