## Description

When initializing Storybook for React Native, users must manually update their Metro bundler configuration file to integrate Storybook. This is a friction point that causes setup failures for many developers: the required changes are non-obvious, the file may use different module formats (CommonJS vs ESM), and the init flow doesn't guide the user through making them.

We should add an automated codemod that detects the project's Metro config file, determines the module format it uses, and automatically wraps the exported configuration object with the Storybook integration helper — injecting the appropriate import at the top. The codemod should be part of the React Native generator and run during Storybook initialization.

## Expected Behavior

- The codemod transforms the Metro config to wrap the exported configuration and adds the Storybook integration import.
- It handles both CommonJS and ESM module styles, including TypeScript configurations.
- If the config already has the Storybook integration, the codemod skips without modifying the file.
- Running the codemod twice should be safe and produce no duplicate changes.
- If multiple Metro config files are present, the user is prompted to choose which one to modify.
- For Expo-based projects, the codemod attempts to generate the Metro config automatically via the Expo CLI before proceeding.
- If the Metro config is missing and the user is not in non-interactive mode, the user is prompted to provide a custom path.
- If the config's export shape is unrecognized and cannot be automatically transformed, a fallback comment is prepended to guide the developer, and the operation is reported as a fallback.
- Existing import aliases for the Storybook integration helper are detected and reused rather than creating duplicate imports.
- Leading pragmas and directives (such as TypeScript suppression comments or strict-mode declarations) are preserved as the first content in the file after transformation.
- TypeScript type information on exported functions is preserved during wrapping.

## Why This Matters

Manual Metro config modification is a common source of failed Storybook setups in React Native projects. Automating this step makes the initialization experience reliable and removes a confusing post-installation manual step.
