## Description

Cloudflare Pages local development currently does not support reading a project configuration file. Developers working on Pages projects must pass all bindings, environment variables, and settings via command line flags every time they run local development. This is cumbersome for complex projects and inconsistent with how Workers projects are configured.

We should add support for Cloudflare Pages local development to read and use a standard configuration file placed at the project root. This would allow developers to define their environment variables, KV namespaces, D1 databases, R2 buckets, service bindings, Durable Object bindings, and AI bindings in a config file, which is automatically picked up when running local development. Command line arguments should still take precedence over configuration file values when both are provided, enabling per-run overrides.

## Expected Behavior

- Running Pages local development reads the configuration file from the project root and applies all top-level bindings and settings to the worker
- Environment variables and all binding types defined in the configuration file are available to the worker at runtime
- When both configuration file settings and command line flags are supplied, command line flags win (override), while non-conflicting config file entries are merged in
- If no directory, proxy command, or proxy port is specified but a configuration file is present with the output directory configured, Pages dev should work without explicit CLI args; the "missing args" error message should reflect this new option
- Using a custom configuration file path via a flag should produce a clear error explaining that only non-default (custom) paths are unsupported, not configuration files in general
- Validation error messages for unsupported environment names should quote each name individually and use clear, properly-indented formatting
- Both Pages Functions projects and Pages Advanced Mode projects should work correctly with a configuration file, including proper application of custom routing rules

## Why This Matters

This feature closes a significant gap in developer experience for Pages projects, allowing teams to check their configuration into version control, avoid repetitive CLI flag entry, and align their Pages workflow more closely with the Workers development experience.
