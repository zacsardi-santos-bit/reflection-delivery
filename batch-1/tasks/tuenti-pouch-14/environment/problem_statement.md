## Description

Currently, when a secret is refreshed, the files that depend on it are updated in an indeterminate or alphabetical order. There is no way to control which files get written first. In certain deployment scenarios, writing files in a specific sequence is important — for example, a certificate chain must be fully written before a service reloads.

Additionally, the configuration structure for specifying output files is tightly coupled to individual secrets, making it awkward to define shared or independent file outputs.

## Expected Behavior

- Files should support an optional numeric priority value in the configuration. A lower number means the file is written earlier. When no priority is given, a sensible default is used.
- When multiple files share the same priority, they should be sorted alphabetically by their path to provide a stable, deterministic order.
- The file list configuration should be expressible at the top level of the configuration file, decoupled from any single secret definition.
- The priority ordering must be preserved correctly through state persistence: saving the state to disk and reloading it should produce an equivalent ordering.
- When state is loaded from an existing file (which does not store explicit priorities), files should be assigned priorities based on the order they appear, so the effective ordering is preserved.

## Why This Matters

In production environments, the order in which configuration files are written can affect service startup or reload behavior. Having a deterministic, user-controllable ordering ensures that dependent files are always written in the correct sequence, reducing race conditions and configuration errors during secret rotation.
