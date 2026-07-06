## Description

The infrastructure apply subcommand in the developer CLI tool is currently a stub — it immediately returns a "not implemented" error. We need to implement it so that users can actually apply infrastructure configuration changes to a named environment (called a "biome").

Part of the challenge is that the underlying infrastructure tool requires an initialization step before it can apply any changes. If a user runs the apply subcommand on a fresh environment, the initialization step hasn't been done yet. Rather than making users manually perform that prerequisite step, the apply command should automatically detect whether initialization is needed and run it transparently before proceeding.

## Expected Behavior

- The apply command accepts a single biome name as its argument and applies the infrastructure configuration for that biome.
- Before running the apply step, the command checks whether the environment has been initialized. If not, it initializes it automatically.
- If initialization has already been done, it is skipped (no duplicate work).
- A helper utility should resolve the filesystem path to a named biome directory given the module root.
- Test setup code should be refactored into a shared helper that creates a temporary module directory (with a module declaration file) and returns a cleanup function.

## Why This Matters

Without this, users must know to manually run initialization before every fresh apply, and the apply command itself is non-functional. With this change, the apply subcommand works end-to-end from a clean state.
