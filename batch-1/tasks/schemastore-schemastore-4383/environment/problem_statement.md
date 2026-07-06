# Add schema support for gematik Tiger test environment configuration

## Description

Developers using the gematik Tiger test platform write YAML configuration files to define their test environments — specifying proxy settings, server definitions, routing rules, and other parameters. Currently, SchemaStore has no schema covering these configuration files, so editors cannot provide autocompletion, inline documentation, or validation feedback when users are writing them.

## Expected Behavior

- A schema should be added to SchemaStore that covers the Tiger test environment configuration format.
- The schema should be automatically recognized for files following the standard Tiger configuration naming convention.
- Once the schema is in place, editors should be able to validate configuration structures including proxy settings, server maps, routing configuration, and environment-level options.
- The schema should be permissive enough that additional top-level configuration keys used by Tiger (such as global settings and plugin-specific sections) do not cause false validation errors.

## Why This Matters

Without this schema, Tiger users get no editor assistance when writing their configuration files, making it easy to introduce typos or structural mistakes. Adding the schema means users get real-time validation and autocompletion across all Tiger configuration files that follow the standard naming convention.
