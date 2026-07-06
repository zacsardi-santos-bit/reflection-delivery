## Description

When a TypeScript library project does not specify an explicit output directory in its compiler configuration, the build plugin currently uses an incorrect or overly narrow set of output paths to track what files the build will produce. This causes task caching and output tracking to be unreliable for such projects — either marking too many or too few files as build outputs.

Additionally, the logic for determining whether a package is actually configured for building (as opposed to just being a source-only library) is not properly exported or testable as a standalone utility, making it harder to maintain and extend.

## Expected Behavior

- When a TypeScript project's compiler configuration has no explicit output directory, the inferred build outputs should cover all common compiled artifact patterns: JavaScript files, source maps, declaration files, declaration maps, and build info files — all scoped to the project root.
- A dedicated utility function should be available for validating whether a package has a build configuration consistent with published entry points (i.e., whether its package export configuration or legacy entry point field point to compiled output rather than TypeScript source files).
- This validation should correctly handle the full variety of modern package export formats, including export maps with multiple conditions, null entry points, wildcard patterns, and the legacy entry point field.
- The validation should account for all three scenarios: when an explicit output file is configured, when an explicit output directory is configured, and when neither is configured (falling back to checking whether entry points reference non-source files).

## Why This Matters

Without these fixes, users with packages that compile output inline alongside sources may experience incorrect caching behavior — builds may be re-run unnecessarily or outputs may not be properly tracked. Extracting the validation logic into a properly typed, testable utility also makes the plugin easier to maintain and extend over time.
