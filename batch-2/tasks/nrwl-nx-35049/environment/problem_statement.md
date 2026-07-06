## Description

The Angular plugin's Tailwind CSS glob-pattern utility is being deprecated because newer versions of Tailwind CSS no longer require these glob patterns for content detection. However, many users still call this utility in their Tailwind configuration files, and they currently receive no indication that it is deprecated.

We need to add a deprecation warning that fires when the utility function is used, so developers know they should migrate away from it. The warning should mention that the module is deprecated and will be removed in an upcoming major version.

## Expected Behavior

- When a developer calls the glob-pattern utility from the Angular Tailwind module, a warning should be logged to the console indicating that the module is deprecated.
- The warning must only appear **once per process run**, even if the function is called multiple times (e.g., when multiple Tailwind config files are processed or when the function is called in a loop). Repeated warnings would clutter build output.

## Why This Matters

Without a deprecation warning, developers have no indication that they need to update their Tailwind configuration. Adding a single, clear warning when the function is first used lets them know to migrate, without spamming the console on every build.
