Implement accessibility enhancements for the Avatar and Badge components in a React Native UI library. Ensure the Avatar is treated as a single accessible image and the Badge's text is explicitly accessible.

*   Update the Avatar component:
    *   Set the root container's `accessible` property to `true`.
    *   Assign the `accessibilityRole` of the root container to `"image"`.
    *   Set the `accessibilityLabel` of the root container to default to an empty string `""` if no label prop is provided.
    *   Render the inner container view with `accessible` set to `false` to exclude it from the accessibility tree.

*   Update the Badge component:
    *   Ensure the text element is rendered with `accessible` set to `true`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.