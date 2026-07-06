## Description

The IDE sidebar navigation button component currently lives inside the application layer, tightly coupled to application-specific utilities and test infrastructure. This makes it difficult to reuse across different parts of the codebase and means consumers must depend on application-layer imports to get a shared UI building block. The component's type definitions are also bundled together with its implementation rather than being in a separate, importable types file.

We need to move this sidebar button component into the shared design system library so it can be consumed by any part of the application (or other packages) without pulling in application-specific dependencies. The type definitions should be extracted into their own file for clearer organization.

## Expected Behavior

- The sidebar button component is available as a named export from the design system library at the appropriate path under the Sidebar templates directory
- The component's prop types are exported from a separate types file in the same directory
- The condition-state enum (used to signal warning states on buttons) is defined and exported from a shared enums file in the Sidebar templates directory
- When a condition is applied to the button (e.g., a warning), the corresponding indicator element is rendered with a predictable DOM identifier
- Clicking an already-selected button does not trigger the navigation callback
- Clicking an unselected button triggers the navigation callback with the button's URL suffix

## Why This Matters

Having the sidebar button in the shared design system package lets teams build on it without coupling to the application layer, reduces duplicate code, and makes testing more straightforward with standard testing library utilities.
