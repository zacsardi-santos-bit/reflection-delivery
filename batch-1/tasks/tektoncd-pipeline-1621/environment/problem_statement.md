## Description

The pod-building logic in Tekton pipeline is split across two packages. Core pod-construction helpers live in the pod package, but the main pod assembly function lives in the reconciler's resources package. This split forces many helper functions and their associated volume configurations, constants, and annotation keys to be exported even though they are only ever used within the pod-building code — not by external callers.

## Expected Behavior

- All pod-construction code should live in a single package so internal helpers can remain private
- Helper functions for ordering containers, converting scripts, initializing credentials, resolving entrypoints, and setting up working directories should be internal to the package
- Volume mounts, volumes, annotation keys, and container name prefixes that are only used internally should not be part of the public API
- The three image references used by helper containers (credentials initializer, entrypoint, and shell) should be grouped and passed as a single structured value rather than as individual strings
- The label keys used to identify pods managed by the task run reconciler should be accessible from the pod package

## Why This Matters

Unnecessarily exporting symbols forces external packages to depend on internal details that may change, making future refactoring harder. Consolidating this code into one package with a minimal public surface makes the architecture cleaner and reduces unintended coupling between the reconciler layer and low-level pod-building concerns.
