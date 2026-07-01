## Description

Static feedback components — such as programmatic toast messages, notifications, and confirm dialogs — cannot currently be configured through a global theming wrapper. This means that applications using custom prefix classes, icon prefix classes, RTL layouts, or localized text cannot apply those settings to static API calls the same way they can for component-based usage.

## Expected Behavior

- The global configuration API for the design system's provider should accept a render wrapper option that allows developers to wrap the internal container of all static feedback APIs.
- When a custom class prefix or icon prefix is set in this wrapper, static messages, notifications, and modals should render using those custom class names rather than the defaults.
- RTL layout direction set via the wrapper should be reflected in the rendered output of all static feedback elements.
- Locale settings (such as localized button labels for confirm dialogs) passed via the wrapper should be applied to dialogs triggered by the static API.
- Constraints such as maximum visible item count, when applied via the wrapper, should be respected.
- A well-defined priority system should govern how settings from different levels (global config, wrapper, component-specific config) interact with each other, with the most specific level winning.

## Why This Matters

Many real-world applications call the static message and notification APIs but need them to match custom theming or layout direction settings. Without this capability, developers are forced to work around the limitation by rewriting their code to use component-based APIs instead, which is significantly more complex. With this change, a single global configuration point can ensure all static feedback respects the application's theme and locale.
