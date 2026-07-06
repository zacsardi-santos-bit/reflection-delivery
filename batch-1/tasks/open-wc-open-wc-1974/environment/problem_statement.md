## Description

The markdown docs addon package generates code that attaches story metadata to Storybook exports using a deprecated, nested API format. Specifically, story parameters and story names are being nested under an intermediate property on each exported function, which no longer matches the current Storybook API expectations.

Additionally, generated documentation pages reference a legacy prebuilt library that has been superseded. The import paths need to be updated to point to an updated, project-type-aware prebuilt library, and the functions involved need to accept the project type as a parameter so they can generate the correct import paths.

## Expected Behavior

- When code is generated for a story, parameters should be attached directly to the exported function rather than through a nested intermediate object.
- When a story has a custom display name, that name should also be attached directly to the exported function rather than through a nested intermediate object.
- No intermediate wrapper object should be created or referenced when assigning story metadata.
- The docs page code generator should accept the project type and use it to produce the correct import paths for the prebuilt storybook library.
- The docs-only page marker should be attached as a flat parameters object directly on the export, not nested.
- The main transformation function should accept the project type as an explicit argument so it can be passed through to the docs page generation step.

## Why This Matters

Storybook's Component Story Format (CSF) evolved to support attaching story parameters and names directly to exported functions rather than using a nested intermediate property. Generating code in the old format may cause stories to not display correctly or lose their configuration in current versions. Likewise, using the wrong import paths prevents the docs addon from functioning correctly.
