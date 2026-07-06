Implement a custom margin feature for the Divider component to allow developers to specify the margin between the divider label and its nearest edge when aligned to the left or right. Update the component to accept a new optional prop, `orientationMargin`, which overrides the default margin and applies specific styles and classes.

*   Update the Divider component in `components/divider/index.tsx`:
    *   Accept an optional `orientationMargin` prop of type `string | number`.
    *   When `orientation` is `"left"` and `orientationMargin` is provided:
        *   Add the CSS class `ant-divider-no-default-orientation-margin-left` to the container `div`.
        *   Set the inner text `span` inline style `marginLeft` to the `orientationMargin` value.
    *   When `orientation` is `"right"` and `orientationMargin` is provided:
        *   Add the CSS class `ant-divider-no-default-orientation-margin-right` to the container `div`.
        *   Set the inner text `span` inline style `marginRight` to the `orientationMargin` value.

*   Update the `DividerProps` TypeScript interface in `components/divider/index.tsx`:
    *   Include `orientationMargin?: string | number`.

*   Update the demo file `components/divider/demo/with-text.md`:
    *   Add an example with `orientation="left"` and `orientationMargin="0"` (string zero).
    *   Add an example with `orientation="right"` and `orientationMargin={50}` (numeric 50).

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.