## Description

There is currently no built-in way for Storybook's development server to automatically generate a new story file for an existing component. Developers must manually write boilerplate story files, which is tedious and error-prone. This feature adds a server-side capability to generate properly formatted story files on demand, supporting both TypeScript and JavaScript components, and returning the resulting story identifier for immediate navigation.

## Expected Behavior

- A developer (or tool) can trigger story file creation by providing a component's file path, its exported name, and whether it uses a default or named export.
- The system must automatically detect whether the component is TypeScript or JavaScript and produce the correct file format.
- For TypeScript components, the generated file must include proper type annotations, type-checked meta declarations, and type-annotated story exports.
- For JavaScript components, the generated file must include clean imports and an untyped meta/story structure.
- On success, the system returns a unique story identifier derived from the file path and story name.
- On failure, a descriptive error message must be surfaced explaining what went wrong.
- A utility must be available for converting arbitrary strings (including those with leading digits, hyphens, spaces, or special characters) into valid PascalCase variable names suitable for use in generated code.
- A utility must be available for normalizing file path separators to forward slashes, regardless of the operating system.

## Why This Matters

This feature enables tools and IDE integrations to programmatically create story files without requiring developers to write boilerplate manually. It provides a consistent, correct output regardless of the component's language or export style.
