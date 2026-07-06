## Description

Storybook's server-side processing needs a reliable way to extract structured metadata from documentation files written in the MDX format. These files contain a special metadata component that declares things like the story title, which component is being documented, a display name, a summary description, whether the file is a reusable template, and a set of tags. Currently, there is no dedicated utility to parse this metadata out of an MDX document, and there is no validation of the metadata attributes.

## Expected Behavior

- A utility that parses an MDX document and returns structured metadata including: the list of imports, the story title, the referenced component (resolved to its import path), a display name, a summary, a template flag, and tags.
- String-only attributes (title, display name, summary) should only accept plain string values. Using a dynamic expression (like a template literal) for these fields should produce a clear error indicating the field name and the type of value that was received.
- The component reference attribute must be a JSX expression pointing to a known imported identifier; providing a plain string or an unknown identifier should produce a clear, descriptive error.
- The template flag should support an implicit boolean (bare attribute), explicit boolean expressions, and should reject string literals or non-boolean expressions with clear error messages.
- The tags attribute must be an array of plain string values; a non-array value or non-string array elements should each produce a descriptive error.
- If the metadata component appears more than once in the document, an error should be raised indicating it can only be declared once.
- If the document contains no metadata component, the utility should return default/empty values rather than an error.
- Malformed MDX that prevents parsing the metadata component should resolve gracefully, returning whatever imports were found and default values for metadata fields.
- The utility should not fail on documents that contain exported declarations.

## Why This Matters

Having a validated, server-side metadata extraction utility makes it possible for Storybook to reliably index and process documentation pages. Clear, early error messages make it easier for authors to diagnose problems in their documentation files rather than encountering cryptic failures later in the build or at runtime.
