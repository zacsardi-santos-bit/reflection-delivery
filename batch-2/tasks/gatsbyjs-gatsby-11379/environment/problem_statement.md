## Description

The Gatsby Google Tag Manager plugin currently loads GTM on pages but has no way to set up a default data layer before GTM initializes. This is a common analytics requirement — many implementations need certain contextual data (like page type or platform information) to already be in the data layer before any GTM tags fire. Without this capability, developers must resort to custom scripts or workarounds outside the plugin.

## Expected Behavior

- The plugin should accept an optional default data layer configuration that can be either a static key-value object or a function that runs in the browser.
- When a static object is provided, its contents should be pushed to the data layer before the GTM loader script runs.
- When a function is provided, it should be evaluated at runtime and the result pushed to the data layer before GTM loads.
- If the provided default data layer is of an unsupported type or is not a plain object when one is expected, the plugin should report a clear error rather than silently producing broken output.
- When no default data layer is configured, no data layer setup code should appear in the page HTML.
- The GTM script and any data layer initialization code should be rendered as a single unbroken line with no newline characters.

## Why This Matters

Many analytics setups rely on having certain data available from the very first GTM event. Without first-class support for a configurable default data layer, users of this plugin cannot reliably ensure that GTM has the context it needs at initialization time.
