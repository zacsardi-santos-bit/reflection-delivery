Implement support for configuring focus behavior in the ContextualMenu component by adding two optional boolean props. Ensure these props control whether the menu automatically receives focus on mount and whether its inner container is independently focusable.

*   Update the `ContextualMenuProps` interface in `packages/components/ContextualMenu/src/ContextualMenu.types.ts`:
    *   Add `shouldFocusOnMount?: boolean` with a default value of `true` to control the root Callout's initial focus behavior.
    *   Add `shouldFocusOnContainer?: boolean` with a default value of `false` to control the accessibility and keyboard focus of the inner container View.

*   Update the `ContextualMenuSlotProps` interface in `packages/components/ContextualMenu/src/ContextualMenu.types.ts`:
    *   Add a `container` slot typed as `ViewProps` from 'react-native'.

*   Modify the `ContextualMenu` component in `packages/components/ContextualMenu/src/ContextualMenu.tsx`:
    *   Destructure `shouldFocusOnMount` (default `true`) and `shouldFocusOnContainer` (default `false`) from `userProps`.
    *   Pass `setInitialFocus: shouldFocusOnMount` to the root slot props.
    *   Pass `accessible: shouldFocusOnContainer` and `acceptsKeyboardFocus: shouldFocusOnContainer` to the container slot props.
    *   Add a `container` slot using `View` from 'react-native' with an empty styles array.
    *   Render the component structure as `<Slots.root><Slots.container>{children}</Slots.container></Slots.root>`.

*   Ensure default rendering behavior:
    *   With default props, the root Callout should have `setInitialFocus={true}`.
    *   The child View should have `acceptsKeyboardFocus={false}` and `accessible={false}`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.