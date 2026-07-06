## Description

Storybook is introducing a new factory-based story format, where meta and story declarations are written using factory method calls rather than plain object exports with type annotations. We need an automated migration tool that can transform existing story files into this new format.

Currently, developers who want to adopt the new story format must manually rewrite their story files, which is error-prone and time-consuming — especially for large projects. This migration should handle a wide variety of existing story patterns automatically.

## Expected Behavior

The migration tool should:

- Transform const-declared meta variables followed by a default export into the new factory call syntax
- Handle inline default-exported objects (no intermediate variable)
- Rename any meta variable that isn't already called "meta" to "meta"
- Wrap each named story export object in a factory story method call
- Add the necessary import from the storybook preview config, merging it into any existing import from that path
- If a local variable named "config" already exists in the file, use an alias for the imported configuration to avoid name conflicts
- Convert older function-style stories (where the export value is a function rather than an object) into the new factory story format with a render property
- Strip all TypeScript type annotations (both the type assertion and the type satisfaction annotation variants) from meta and story declarations, since types are now inferred
- Remove type imports from storybook packages that are no longer needed
- Produce identical output regardless of which TypeScript annotation syntax variant was used

## Why This Matters

Manually migrating dozens or hundreds of story files is unreliable and discourages adoption of the new format. An automated codemod allows teams to migrate their entire story library in one step, confidently and consistently.
