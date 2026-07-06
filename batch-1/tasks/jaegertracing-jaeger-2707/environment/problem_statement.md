## Description

The Jaeger Query UI configuration loading currently only supports JSON files. Users who want to use a JavaScript-based configuration — for example, to define configuration as a function for more dynamic or conditional setups — have no supported way to do this. We should support JavaScript configuration files in addition to JSON.

## Expected Behavior

- When a JavaScript configuration file is provided, the handler should validate that the file properly defines the required configuration function. Files that do not define the function should be rejected with a descriptive error message.
- When a JSON configuration file is provided, behavior should remain unchanged — the configuration should be embedded in the page as before.
- When no configuration file is provided at all, a default configuration should be embedded in the page without any error.
- When a configuration file with an unsupported extension (neither JSON nor JavaScript) is provided, the error message should clearly indicate which formats are accepted.

## Why This Matters

Users need the ability to define UI configuration using JavaScript so they can express more complex or conditional configurations. The current JSON-only approach is too restrictive for some deployment scenarios, and the unhelpful error messages when providing unsupported formats make configuration troubleshooting harder.
