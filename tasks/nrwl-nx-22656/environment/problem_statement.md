## Description

After upgrading rollup to a recent major version, the Nx rollup plugin can no longer automatically infer build targets from rollup configuration files. The plugin relied on a config-loading utility accessed through an internal path that only existed in older versions of rollup. In the new version, that path is gone and the equivalent functionality is exposed through an official public API path instead.

## Expected Behavior

- The Nx rollup plugin should correctly load rollup config files using the public API provided by the current rollup version.
- Nx should be able to automatically discover and configure build targets for projects that use rollup, regardless of whether the config defines a single output or multiple outputs.
- Both root-level and nested (non-root) project configurations should work correctly.

## Current Behavior

The plugin fails to load rollup config files because it attempts to access an internal module path that no longer exists in the newer rollup version. This means Nx cannot infer build targets from rollup configs, breaking automatic project configuration for rollup-based libraries.

## Why This Matters

Teams that have upgraded rollup lose automatic build target inference from their existing rollup config files. They can no longer rely on Nx to automatically detect their build setup, requiring manual configuration as a workaround.
