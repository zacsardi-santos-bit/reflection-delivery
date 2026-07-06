## Description

The Gatsby Less plugin currently compiles Less files without any support for variable overrides at build time. This makes it impossible to use design-token-based theming with component libraries that rely on Less variable customization. Developers who want to change things like colors or font sizes provided by a Less-based component library have no way to do so through the plugin — they would have to resort to overriding compiled CSS, which is fragile and cumbersome.

## Expected Behavior

- The plugin should accept an optional theme configuration when it is registered as a plugin in the project configuration.
- The theme can be specified as a plain JavaScript object containing key-value pairs representing Less variable overrides.
- The theme can also be specified as a path to a JavaScript file that exports such an object — making it easy to share the theme definition across the project.
- When a theme is provided, the Less compiler should apply those variable overrides during the build, merging them into every Less file being compiled.
- When no theme is provided, the plugin should continue to work exactly as before.
- In development mode, source maps should still be enabled regardless of whether a theme is configured.

## Why This Matters

Many popular component libraries expose their design tokens as Less variables. Without the ability to override those variables at compile time, developers using such libraries with Gatsby cannot customize the look-and-feel of the library through the official mechanism. This feature makes the Gatsby Less plugin first-class for these use cases.
