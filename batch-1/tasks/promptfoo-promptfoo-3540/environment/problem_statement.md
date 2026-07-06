## Description

When configuring a Python provider, all configuration values must currently be inlined directly in the config block. There is no way to reference external files (JSON, YAML, JavaScript modules, Python scripts, or text files) from within the provider configuration. This makes it difficult to share configuration across providers, keep large configurations manageable, or dynamically generate configuration values using code.

## Expected Behavior

- Provider configurations should support a file-reference URL syntax that points to an external file
- When the provider initializes, all file references should be resolved to their actual content before the Python script runs
- File references should support JSON, YAML, JavaScript, Python, and plain text files
- JavaScript and Python file references should support specifying a named function to call using a colon-separated suffix
- File references should be resolvable relative to a configured base path
- Nested file references inside objects and arrays should all be resolved
- The resolved configuration values should be passed to the Python script — not the raw file path strings
- If a referenced file cannot be loaded, initialization should fail with a clear error
- Initialization should only perform file resolution once, even if the provider is used multiple times

## Why This Matters

This allows developers to manage complex Python provider configurations using separate files, share configuration between providers, and use code (JavaScript or Python) to dynamically generate configuration values. It keeps the main configuration file clean and makes configurations more reusable and maintainable.
