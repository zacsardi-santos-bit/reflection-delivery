Implement a set of overflow components for a React Native UI library to manage items that don't fit within a container's width, displaying them in a dropdown menu. Create a container component, an item wrapper component, and a hook to facilitate this functionality.

*   Export the following from `packages/experimental/Overflow/src/index.ts`:
    *   `Overflow` component
    *   `OverflowItem` component
    *   `useOverflowMenu` hook

*   Implement the `Overflow` component:
    *   Accept an `itemIDs` prop (array of strings) and standard View props.
    *   Render children inside a View container with styles: `display: 'flex'`, `flexDirection: 'row'`, `flexWrap: 'wrap'`, `opacity: 0` (before layout calculation), and `paddingHorizontal: undefined` when no padding is provided.
    *   Include an `onLayout` callback in the container.

*   Implement the `OverflowItem` component:
    *   Accept an `overflowID` string prop and optionally a `priority` number.
    *   Render its single child directly by cloning it with injected style and `onLayout` props, without adding an additional wrapping View.

*   Implement the `useOverflowMenu` hook:
    *   Return an object with:
        *   `showMenu`: boolean, true by default before initial layout and when items overflow.
        *   `visibleMenuItems`: string array, listing IDs of items hidden in the container.
        *   `menuTriggerRef`: React ref for the menu trigger element.
        *   `onMenuTriggerLayout`: callback for the menu trigger's layout event.
    *   Ensure `showMenu` is true and `visibleMenuItems` is an empty array before initial layout calculation.

*   Ensure the `Overflow` component tree:
    *   Produces consistent styling across multiple render passes.
    *   Re-renders correctly and produces the same output with the same props.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.