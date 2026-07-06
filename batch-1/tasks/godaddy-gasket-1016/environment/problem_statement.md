## Description

The Gasket framework has a plugin that supports loading additional plugins dynamically at startup, and a separate plugin that handles CLI command registration. Currently, command registration happens too early in the startup sequence, before dynamically loaded plugins have been registered. This means dynamic plugins have no opportunity to contribute their own CLI commands.

Additionally, dynamically loaded plugins are only partially initialized — they only run through two of the three standard setup phases. This leaves dynamic plugins in an incomplete state compared to plugins registered statically.

## Expected Behavior

- Command registration should be moved to a later lifecycle phase, after dynamic plugins have been loaded, so that dynamic plugins can register their own commands
- The configure phase should only be responsible for identifying the current command and applying any command-specific configuration overrides — the command-specific overrides object from config should be consumed and applied during this step
- Dynamically loaded plugins should run through all three standard setup phases (not just the first two), so they are fully initialized
- When a lifecycle call is skipped for an already-initialized static plugin during dynamic plugin setup, a trace message indicating deduplication should be emitted
- Plugins specified in the dynamic plugin list using relative file paths should be resolved against the application root directory

## Why This Matters

Without this change, users cannot write dynamic plugins that register their own CLI commands. The dynamic plugin feature is also incomplete because dynamically loaded plugins miss a full initialization phase, which can cause subtle bugs if those plugins rely on the third phase to complete their setup.
