Implement the accessibility tap gesture handling in the Text component of the React Native design system. Ensure that the component responds to accessibility tap gestures by invoking the appropriate press handler, and allow for custom accessibility tap callbacks.

*   Modify the Text component located at `packages/experimental/Text/src/Text.tsx`:
    *   Ensure the component renders with an `onAccessibilityTap` prop that is always a function.
    *   If `onAccessibilityTap` is not explicitly provided, default it to a callback that invokes the `onPress` handler.
    *   Use the explicitly provided `onAccessibilityTap` prop if available.
    *   Pass the resolved `onAccessibilityTap` function to the underlying rendered text element.

*   Ensure all components using the Text component (such as Menu, Notification, Badge, MenuButton, and Tabs) automatically benefit from this implementation without requiring changes.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.