## Description

When a Prefect runner pulls a deployment whose code includes a Python project definition, it currently tries to automatically detect a compatible package manager and use it to install and manage dependencies before starting the flow run. This happens unconditionally — there is no way to disable it if you have a pre-built environment that already contains the required packages.

This creates a problem for users who build custom Docker images with all dependencies already bundled: the runner may still attempt to install packages at runtime, potentially causing unexpected behavior, overwriting carefully pinned versions, or adding install overhead on every flow run startup.

## Expected Behavior

- By default, the runner should **not** attempt to install or prepare dependencies when a flow run is starting. It should simply start the flow with whatever environment is already present.
- Users who do want runtime dependency preparation should be able to opt into this behavior explicitly via a configuration setting.
- When the opt-in setting is enabled and a compatible package manager is used, only the core project dependencies should be installed — optional/default dependency groups should be excluded.

## Why This Matters

Users deploying flows in custom images with pre-installed dependencies need a predictable, reproducible runtime. Automatic dependency installation introduces nondeterminism, can slow down flow startup, and can conflict with carefully managed image contents. Making this behavior opt-in ensures existing workflows continue to work as expected while still supporting the use case of pulling and running code that needs dependencies prepared at runtime.
