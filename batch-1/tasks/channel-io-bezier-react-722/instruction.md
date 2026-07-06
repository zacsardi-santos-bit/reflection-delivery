Implement an active state for the navigation group component in the Navigator to visually indicate when a group is selected. Extend the NavGroupProps interface and update the NavGroup component to render differently based on this active state.

*   Extend the NavGroupProps interface:
    *   Add an `active` boolean property to indicate if the navigation group is currently active.
    *   Use `Pick<ActivatableProps, 'active'>` to integrate the `active` prop.
    *   Ensure compatibility with `Partial<NavGroupProps>` to prevent TypeScript errors when constructing partial props.

*   Update the NavGroup component:
    *   Accept the `active` prop and apply visual styling based on its value.
        *   When `active` is true, render the group header with a highlighted style (distinct background and text color).
        *   When `active` is false, render the group header with default styling, including hover and focus-visible states.
    *   Ensure the component renders any content passed via the `rightContent` prop on the right side of the group header.
    *   Verify that both `active: true` and `active: false` states render correctly when `rightContent` is provided.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.