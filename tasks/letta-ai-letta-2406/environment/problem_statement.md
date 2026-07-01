## Description

When configuring a local tool execution sandbox, there is currently no way to declare which Python packages should be installed in the virtual environment. If a tool depends on a package that is not already present in the environment — and is not listed in a requirements file bundled with the code — the tool will fail at runtime with a missing module error. There should be a way to specify a list of required packages, with optional version pinning, directly as part of the sandbox configuration so that the virtual environment is automatically provisioned with the correct dependencies.

## Expected Behavior

- Sandbox configurations for local execution should support a list of pip package requirements, where each entry has a mandatory package name and an optional version.
- A local sandbox configuration should be creatable without specifying a sandbox directory — there should be a sensible default (falling back to a configured path or a well-known default location).
- A REST API endpoint should allow creating or updating a custom local sandbox configuration, including any pip requirements, and return the persisted configuration.

## Why This Matters

Without this feature, tools that rely on third-party packages not bundled with the codebase cannot be run in local sandboxes without manual environment setup. Developers need a first-class way to declare package dependencies in the sandbox configuration so the environment is always set up correctly.
