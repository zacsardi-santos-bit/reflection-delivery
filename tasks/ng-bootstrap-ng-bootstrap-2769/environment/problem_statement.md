# Convert API Documentation Tool to TypeScript

## Description

The API documentation extraction tool in this project is currently written as a plain JavaScript file using the older CommonJS module format. Now that the project has fully adopted TypeScript, this tool should be converted to TypeScript so it integrates cleanly with the rest of the toolchain and can be imported using standard module import syntax.

## Expected Behavior

- The API documentation extraction utility should be a TypeScript file, not a JavaScript file
- The main extraction function should be accessible via a named export so it can be imported directly using standard TypeScript/ES module import syntax
- All existing documentation extraction capabilities must continue to work correctly after the conversion, including extraction of directives, components, services, interfaces, inputs, outputs, methods, properties, type parameters, and JSDoc tags such as deprecation and feature introduction information

## Why This Matters

Using the CommonJS require approach to load a JavaScript file from within a TypeScript project creates an inconsistency in the codebase. Migrating the tool to TypeScript with proper named exports allows it to be used uniformly with the rest of the project, enables type checking, and removes the special-case handling needed for the old module format. This makes the build tooling configuration simpler and the overall codebase more consistent.
