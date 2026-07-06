## Description

The Platformatic code generation tools currently have no way to update an existing project without regenerating all files from scratch. When a developer uses the generator on an existing project — for example to add a new plugin or update plugin configuration — it overwrites everything including routes, custom plugins, and other files the developer has created and customized. There needs to be a way to run the generator in "update mode" that reads the current project's configuration from disk and only writes back the minimum necessary changes (the project config file), leaving all other files untouched.

## Expected Behavior

- The generator should support an "update mode" flag in its configuration, defaulting to disabled. When enabled, calling the generation step must produce only the project configuration file and nothing else — no routes, no env files, no package manifests.
- A method should be available to load existing service data (template type, plugin configurations, current option values) from an existing project directory on disk.
- New utilities should be available to: parse environment variable files into key-value objects; flatten nested plugin option objects into dot-notation path structures; and identify a service's framework template from its schema URL.
- The utility that formats an object of environment variables as a string should use the OS-native line ending rather than a hardcoded value, ensuring correct behavior across platforms.

## Why This Matters

Developers building and maintaining Platformatic applications need to be able to iteratively refine their projects — adding or updating plugins through the generator — without losing custom code they've already written. Without an update mode, the generator is a one-time bootstrapping tool rather than an ongoing project management aid.
