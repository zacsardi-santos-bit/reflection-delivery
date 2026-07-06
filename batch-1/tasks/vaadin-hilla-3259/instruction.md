Implement the `createMenuItems` function to generate a navigation menu that excludes routes with dynamic URL segments and their descendants. Additionally, implement the `deepRemoveNullProps` utility function to clean up objects by removing null or undefined properties.

Requirements:

*   Implement `createMenuItems` in `packages/ts/file-router/src/runtime/createMenuItems.ts`:
    *   Exclude routes from the menu if their path contains a variable segment (e.g., '/bar/:id').
    *   Exclude all descendant routes of a variable-segment route (e.g., exclude '/bar/:id/foo' if '/bar/:id' is excluded).
    *   Exclude routes explicitly marked with `{ menu: { exclude: true } }`, but include their children.
    *   Include an `order` field in the `MenuItem` object if a route has a `menu.order` value.

*   Implement `deepRemoveNullProps` in `packages/ts/file-router/test/utils.tsx`:
    *   Signature: `deepRemoveNullProps<T>(input: T): T`
    *   Recursively remove null and undefined properties from objects.
    *   For arrays, filter out null/undefined elements and recursively process remaining items.
    *   For plain objects, remove entries with null or undefined values and recursively process remaining values.
    *   Return primitive values or null inputs unchanged.

*   Ensure `deepRemoveNullProps` is exported from `packages/ts/file-router/test/utils.tsx`.

*   Use the `viewsSignal` from `packages/ts/file-router/src/runtime/createMenuItems.ts` to read route configurations for menu generation.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.