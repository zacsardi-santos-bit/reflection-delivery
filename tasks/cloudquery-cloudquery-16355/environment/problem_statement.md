## Description

The AliCloud source plugin currently has no validation for its configuration specification. When the plugin is initialized with missing or incomplete account credentials, it proceeds without checking if required fields are provided, leading to confusing runtime failures later. There is also no machine-readable schema for the plugin's configuration, making it difficult for tooling to validate user-provided configs without running the plugin.

## Expected Behavior

- The plugin's configuration spec should be validated before use, returning a clear error if any required account field is missing or empty. Specifically:
  - The accounts list must not be absent or empty — at least one account must be provided
  - Each account must have a non-empty name, at least one region, a non-empty access key, and a non-empty secret key
- A JSON Schema representing the valid configuration structure must be available, so that external tools can validate configurations against defined constraints without executing the plugin

## Why This Matters

Catching misconfigurations early (at validation time rather than at runtime) greatly improves the developer experience. The JSON Schema also enables integration with configuration editors and automated validation pipelines that need to understand the plugin's requirements without running it.
