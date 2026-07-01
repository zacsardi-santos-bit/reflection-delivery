## Description

When Flink executes a submitted job, it creates a class loader for the user's code. Currently this class loader always uses a hardcoded child-first strategy — meaning user code classes always take precedence over framework classes — regardless of any configuration the operator has set. There is no way to change this behavior for packaged program submissions, either through the CLI or through the web UI.

## Expected Behavior

- The class loading strategy used when executing packaged programs should respect the configured class loading order setting from the Flink configuration.
- By default (no override), programs should continue to use child-first class loading, where user code is resolved before parent/framework classes.
- When the configuration specifies that the parent-first ordering should be used, the class loader for user programs should resolve framework/parent classes before user code.
- The handler responsible for listing and preparing submitted jars in the web UI should accept and propagate this configuration so that class loading behavior is consistent across all submission pathways.

## Why This Matters

Users who experience class conflicts between their own libraries and Flink's bundled libraries currently have no recourse when submitting packaged programs. Making class loading behavior configurable — and respecting the same configuration options used elsewhere in the system — allows operators to resolve these conflicts and achieve predictable runtime behavior without modifying the application itself.
