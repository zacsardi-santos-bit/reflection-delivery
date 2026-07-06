## Description

The Pulumi Automation API for Go currently treats the CLI binary as a hardwired system dependency — it always discovers and runs whatever CLI binary it finds on the system PATH, with no mechanism to specify a particular installation, pin to a version, or substitute an alternative implementation. This design has two practical consequences:

1. **Testability**: Automation code cannot be unit-tested without a real CLI binary installed. There is no way to inject a lightweight stand-in that simulates CLI responses.
2. **CLI management**: There is no programmatic way to specify or install a particular version of the CLI for use with a given workspace.

Additionally, the version-validation logic is currently embedded inside the workspace initialization, making it harder to reason about and test in isolation.

## Expected Behavior

- The CLI should be represented as an injectable interface so that custom implementations (e.g., test doubles) can be provided to a workspace via a dedicated workspace option.
- A new workspace option should allow callers to supply their own CLI implementation.
- The version validation logic (checking minimum versions, major version mismatches, and unparseable versions) should be available as a standalone function with well-defined error messages.
- When the CLI lives in a custom directory, the automation SDK should ensure that directory is prepended to the process PATH so that plugins and bundled tools are resolved correctly.
- The version check bypass mechanism (via environment variable) should work consistently, including bypassing the remote-operations support check when set.

## Why This Matters

Developers writing automation workflows need to be able to test their code without depending on a live CLI binary. They also need a reliable way to pin the CLI version used by a workspace, especially in CI environments where the system-installed CLI may differ from what the SDK expects.
