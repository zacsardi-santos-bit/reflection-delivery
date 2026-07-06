## Description

The Alert component currently requires a tone to be explicitly provided, but there is no support for a default appearance when no tone is given. This means that any code which renders an alert without specifying a tone will either fail or produce undefined behavior — there is no fallback rendering.

Additionally, the component library's test files are written in plain JavaScript, which means TypeScript type errors in test code go undetected. The shared test utility that iterates over themes and passes a theme-scoped provider to each test block also lacks proper TypeScript types, making it harder to catch prop-type mismatches at compile time.

## Expected Behavior

- The Alert component should render correctly when no tone is provided, falling back to a sensible default appearance.
- The Alert component's prop types should be exported so that they can be referenced in TypeScript code (including tests).
- The theme provider used in test utilities should expose its prop types so typed utilities can use them safely.
- Theme-related types should be importable for use in typed test helpers.
- The test utility that wraps tests in a theme context should be a proper TypeScript module with typed callback signatures.
- All component test files should be TypeScript files.

## Why This Matters

Without a default tone, the Alert component is unnecessarily restrictive. Developers who want to render a neutral alert without a specific tone currently have no supported way to do so. Additionally, the lack of TypeScript in test files means prop-type issues can only be caught at runtime rather than during development.
