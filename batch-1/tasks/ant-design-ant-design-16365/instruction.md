Implement a new feature in the Collapse component to allow control over the position of the expand icon in the panel header. Update the component to accept a new prop and modify the demo to showcase this feature.

*   Update the Collapse component in `components/collapse/Collapse.tsx`:
    *   Add an `expandIconPosition` prop to the Collapse component.
        *   Valid values are 'left' or 'right'.
        *   Default value is 'left'.
    *   Ensure the root container element of the Collapse component includes a CSS class in the format '{prefixCls}-icon-position-{expandIconPosition}'.
        *   Respect any custom CSS prefix configured for the Collapse component.
    *   Add the `expandIconPosition` prop to the `CollapseProps` interface.
*   Export a type alias named `ExpandIconPosition` from `components/collapse/Collapse.tsx`:
    *   Define it as a union type: 'left' | 'right'.
*   Update the `extra.md` demo:
    *   Demonstrate the `expandIconPosition` feature by rendering a Collapse component with the `expandIconPosition` prop.
    *   Include a Select dropdown with 'left' and 'right' options, defaulting to 'left', to control the icon position interactively.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.