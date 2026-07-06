Update the Button component to use a `fullWidth` prop instead of the `inline` prop, ensuring the default behavior is inline. Implement a codemod to automate the migration of existing code using the Button component. Update the InternalLink component to align with these changes.

*   Create a jscodeshift codemod:
    *   Name: `button-replace-inline-fullWidth`
    *   Location: `packages/gestalt-codemods/24.0.0/button-replace-inline-fullWidth.js`
    *   Use the `flow` parser.
    *   Transformations:
        *   Add `fullWidth` to Buttons with no width-related prop.
        *   Remove `inline` shorthand prop.
        *   Replace `inline={false}` with `fullWidth`.

*   Create test fixtures:
    *   Input file: `packages/gestalt-codemods/24.0.0/__testfixtures__/button-replace-inline-fullWidth.input.js`
        *   Include a React component importing Button from 'gestalt'.
        *   Render Buttons with no prop, `inline`, and `inline={false}`.
    *   Output file: `packages/gestalt-codemods/24.0.0/__testfixtures__/button-replace-inline-fullWidth.output.js`
        *   Transform to show `<Button fullWidth />`, `<Button />`, and `<Button fullWidth />`.

*   Update the Button component:
    *   Location: `packages/gestalt/src/Button.js`
    *   Replace `inline` prop with `fullWidth` (default false).
    *   Apply inline CSS class when `fullWidth` is false.
    *   Apply block CSS class when `fullWidth` is true.
    *   Remove `inline` from PropTypes.

*   Update the InternalLink component:
    *   Location: `packages/gestalt/src/InternalLink.js`
    *   Remove `inline` prop.
    *   Use `fullWidth` to control layout:
        *   Apply `inlineFlex` when `fullWidth` is false/absent.
        *   Apply `flex` when `fullWidth` is true.

*   Ensure Button component snapshot tests pass:
    *   Test `iconEnd` rendering with `color`, `iconEnd`, and `text` props without `inline`.

*   Ensure InternalLink snapshot tests pass:
    *   Test with `wrappedComponent="button"` both with and without `fullWidth`.
    *   Verify full-width variant uses flex layout, non-full-width uses inline flex layout.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.