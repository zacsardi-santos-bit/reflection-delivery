Migrate the IDE sidebar navigation button component from the application layer to the shared design system package. Implement the component as a named export, separate its prop types into a distinct file, and define a shared enums file for condition states. Ensure the component interacts correctly with standard testing library utilities.

*   Export the SidebarButton component as a named export from:
    *   `app/client/packages/design-system/ads/src/Templates/Sidebar/SidebarButton/SidebarButton.tsx`.

*   Define and export the SidebarButtonProps interface from:
    *   `app/client/packages/design-system/ads/src/Templates/Sidebar/SidebarButton/SidebarButton.types.ts`.
    *   Include the following fields:
        *   `id: string` (required) — used for the `data-testid` attribute.
        *   `icon: string` (required) — icon name to render.
        *   `title?: string` — optional display title.
        *   `urlSuffix: string` (required) — passed to `onClick`.
        *   `onClick: (urlSuffix: string) => void` (required) — called on click when not selected.
        *   `condition?: Condition` — optional; renders a condition icon when set.
        *   `selected?: boolean` — when true, click does not invoke `onClick`.

*   Define and export the Condition enum from:
    *   `app/client/packages/design-system/ads/src/Templates/Sidebar/enums.ts`.
    *   Include at least:
        *   `Warn = "Warn"`.

*   Ensure the SidebarButton component:
    *   Renders an element with `role="button"` and `data-testid="t--sidebar-{id}"`.
    *   Renders a condition indicator element with `data-testid="t--sidebar-Warn-condition-icon` when `condition` is `Condition.Warn`.
    *   Calls `onClick` with `urlSuffix` when clicked, unless `selected` is true.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.