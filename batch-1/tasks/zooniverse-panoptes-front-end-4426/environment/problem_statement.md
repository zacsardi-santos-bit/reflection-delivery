## Description

The project classification page is currently implemented in a legacy format that is incompatible with standard JavaScript module conventions. This makes it impossible to import and test the component using standard JavaScript tooling, and it also creates friction for future development. We need to migrate the classification page to a standard JavaScript module so that it can be imported, rendered, and tested like any other component in the project.

## Expected Behavior

- The classification page component should be available as a default export from its module, so it can be imported by other JavaScript files and test suites.
- When rendered with a project (and without a logged-in user), the component should display its main container correctly.
- The "project finished" banner should only appear when the project has actually been marked as complete — it should never show up when the project is still active or when the component is rendered without any completion state.

## Why This Matters

Without this migration, the classification page cannot be unit tested, making it harder to catch regressions and verify correct behavior. Converting it to a standard JavaScript module brings it in line with the rest of the codebase and enables automated testing of the component's rendering logic.
