Rename and update the existing stack-layout components by adding a "Legacy" prefix to their names and file paths. Modernize the implementation by using CSS module classes instead of inline styles for layout direction and alignment. Update CSS custom property names to use a consistent, design-system-specific prefix.

*   Implement the `LegacyStack` component:
    *   Render with the CSS module class 'LegacyStack' from `LegacyStack.module.scss`.
    *   Apply 'direction-horizontal' or 'direction-vertical' class based on the `direction` prop.
    *   Support `as`, `style`, and `className` props for customization.
    *   Apply the CSS custom property '--b-margin-before' as an inline style on every `LegacyStackItem` child except the first valid React element child, based on the `spacing` prop.
    *   Skip non-element nodes when determining the first child for spacing purposes.
    *   Export from `packages/bezier-react/src/components/LegacyStack/index.ts`.

*   Implement the `LegacyHStack` component:
    *   Render with the CSS module class 'direction-horizontal'.
    *   Wrap `LegacyStack` with `direction="horizontal"`.
    *   Export from `packages/bezier-react/src/components/LegacyStack/index.ts`.

*   Implement the `LegacyVStack` component:
    *   Render with the CSS module class 'direction-vertical'.
    *   Wrap `LegacyStack` with `direction="vertical"`.
    *   Export from `packages/bezier-react/src/components/LegacyStack/index.ts`.

*   Implement the `LegacyStackItem` component:
    *   Support `as`, `style`, and `className` props for customization.
    *   Apply CSS module classes for `justify` and `align` props using `LegacyStackItem.module.scss`.
    *   Set all five CSS custom properties as non-empty inline style values: '--b-main-axis-size', '--b-grow-weight', '--b-shrink-weight', '--b-margin-before', '--b-margin-after'.
    *   Export from `packages/bezier-react/src/components/LegacyStack/index.ts`.

*   Update SCSS modules:
    *   `LegacyStack.module.scss` must define 'LegacyStack', 'direction-horizontal', and 'direction-vertical'.
    *   `LegacyStackItem.module.scss` must define 'justify-start', 'justify-center', 'justify-end', 'justify-stretch', 'align-start', 'align-center', 'align-end', and 'align-stretch'.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.