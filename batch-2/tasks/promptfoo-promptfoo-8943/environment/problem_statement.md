## Description

When the MCP provider calls a tool, the result only exposes a normalized text string — the original, unprocessed response from the tool is silently discarded. This means it's impossible to access structured or typed output (such as a separate structured content block) that an MCP tool may return. Additionally, there's no way to configure a custom response transformation for MCP tool results, which limits how the output can be shaped before it reaches Promptfoo's evaluation pipeline.

A separate issue is that relative server paths in MCP configurations are not resolved relative to the configuration file's location. When the configuration file is not in the working directory, this causes failures because the server cannot be found.

Finally, some shared transform utilities for handling file-based transform references and for normalizing transform results are duplicated or subtly inconsistent across providers. Utility functions to handle these concerns should be extracted and shared.

## Expected Behavior

- The MCP client should return both the normalized content string and the original raw tool response, so downstream transforms can access structured output.
- The MCP provider should support a response transformation configuration option that accepts a JavaScript expression, a function, or a file reference. The transform receives the raw result, normalized content, and tool call metadata, and may return a reshaped response.
- Provider-controlled metadata (tool name, tool arguments, original payload) must take precedence over any conflicting keys returned by the transform.
- Relative server paths in MCP configurations must be resolved relative to the base path from the CLI state when it is set.
- A shared utility for normalizing transform results should treat any value that already carries a top-level output property as a complete provider response, and wrap anything else in an output wrapper.
- A shared utility for parsing file-based transform references should correctly handle both POSIX and Windows paths, splitting on the last colon to extract an optional named export.

## Why This Matters

Without access to the raw MCP tool response, users cannot take advantage of structured content formats that tools return. The response transform option closes this gap and gives users the flexibility to extract the exact information they need — including metadata, structured fields, or derived values — before the result is evaluated.
