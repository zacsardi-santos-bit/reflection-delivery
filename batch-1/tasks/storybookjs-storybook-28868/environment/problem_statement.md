## Description

The vitest addon currently contains its own internal story file transformer that duplicates logic already available (or better placed) in the shared core tooling. This transformer relies on third-party dependencies for code manipulation and TypeScript AST parsing that are specific to the addon and add unnecessary weight. The transformation logic should be moved to the shared core package so it can be reused, maintained in one place, and benefit from the richer CSF parsing infrastructure already available there.

Additionally, there are two usability issues with the current implementation:

1. When a story test fails, the debug link appended to the error message only includes the Storybook interactions panel shortcut when the story has interactive behavior configured. It should always include this shortcut, since even stories without interactive behavior can fail in ways that are debuggable through that panel.

2. The test runner helper that executes a story test currently requires callers to pass raw module information so it can look up and compose the story at runtime. This is indirect and harder to follow. The helper should instead accept an already-composed story object directly, making the call site simpler.

## Expected Behavior

- Story file transformation is handled by a shared utility in the core CSF tooling package, not a local copy in the addon.
- The shared transformer correctly handles inline and const-declared default exports, adding titles when missing and converting inline exports to const declarations.
- Named story exports receive test wrapper code respecting tag filters and excluded stories.
- Accurate source maps are generated so test failures link back to the correct line in the original story file.
- Story test debug URLs always include the interactions panel link, regardless of whether the story has interactive behavior.
- The story test runner helper takes an already-composed story object and a tag filter, rather than requiring callers to supply composition utilities.

## Why This Matters

Consolidating the transformation into shared tooling removes duplicated code and simplifies the addon. Developers debugging failing story tests always get a direct path to the interactions panel. The test runner API becomes cleaner and easier to use correctly.
