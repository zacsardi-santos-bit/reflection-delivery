## Description

The newer Grid component silently accepts props that were removed when the component was redesigned. When developers accidentally pass legacy props — such as the prop that designated a grid child as an item, the prop that controlled minimum width behaviour, or the per-breakpoint size props — the component renders without any warning. This makes it very difficult to understand why the layout is not working as expected, especially when migrating an existing codebase from the older Grid to the newer one.

## Expected Behavior

- When a developer passes a legacy prop that has been completely removed and is no longer necessary (such as the item marker or the minimum-width toggle), the newer Grid component should emit a clear console warning explaining that the prop has been removed and that it can be safely deleted from the code.
- When a developer passes a legacy breakpoint-based size prop (one named after a theme breakpoint), the newer Grid component should warn that the prop has been removed and direct them to the migration guide for instructions on how to update their code.
- The warning for each prop should only appear once — not on every render — to avoid flooding the developer console.

## Related Changes

The Grid migration guide page has been reorganized and is now located at a new URL path. Any internal tooling that rewrites documentation links must be updated to map references to the new path rather than the old one.

## Why This Matters

Without these warnings, developers migrating from the legacy Grid to the newer Grid component have no way of knowing that certain props they are passing are being silently ignored. The warnings provide immediate, actionable feedback that speeds up migration and reduces confusion.
