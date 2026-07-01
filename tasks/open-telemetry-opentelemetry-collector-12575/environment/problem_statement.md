## Description

An upstream OpenTelemetry contrib library that this project depends on for SDK configuration has been relocated to a new module path. The old module path no longer exists, which means the current codebase fails to compile in the service and telemetry packages.

## Expected Behavior

- All source files that reference the old module path should be updated to use the new module path.
- The dependency declarations in the relevant module files should reflect the new module name and updated version.
- The checksum files should be updated to match the new dependency graph.
- After the update, all existing service telemetry tests — covering attributes, configuration, logging, metrics, and tracing — should pass without any behavioral changes.

## Why This Matters

The rename was made upstream to avoid naming ambiguity. Until this project catches up with that rename, the affected packages cannot be built or tested, blocking development and CI for any change that touches the service telemetry code.
