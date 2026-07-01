Implement a reusable notification banner component for a React Native component library to ensure consistent styling and behavior across applications. The component should accept a visual style variant, a main message, and an action label, rendering them in a stable layout.

*   Create a `Notification` component in `packages/components/Notification/src/Notification.tsx`.
    *   Export the component as a named export.
    *   Accept props: `variant` (with values 'primary', 'neutral', 'danger', 'warning'), `endText` (string), and `children` (React.ReactNode).
*   Render the component with the following structure:
    *   Root View when `variant` is 'primary':
        *   Styles: `alignItems` 'center', `backgroundColor` 'skyblue', `borderRadius` 12, `flex` 1, `flexDirection` 'row', `justifyContent` 'space-between', `padding` 16.
    *   Main message (children) in a Text element:
        *   Properties: `ellipsizeMode` 'tail', `numberOfLines` 0.
        *   Styles: `color` '#323130', `flex` 1, `flexGrow` 1, `fontFamily` 'Segoe UI', `fontSize` 16, `fontWeight` '400', `margin` 0.
    *   End action label (endText) in a separate Text element:
        *   Properties: `ellipsizeMode` 'tail', `numberOfLines` 0.
        *   Styles: `color` '#323130', `fontFamily` 'Segoe UI', `fontSize` 16, `fontWeight` '500', `margin` 0, `marginLeft` 34.
*   Ensure the component's styling remains stable across renders with unchanged props.
*   Verify the component re-renders correctly without errors when props are unchanged.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.