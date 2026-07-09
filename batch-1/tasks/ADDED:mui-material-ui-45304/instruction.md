Implement console warnings in the Grid2 component to notify developers when legacy props are used. Ensure these warnings are clear, specific, and only appear once per application session to prevent console spam.

*   Emit a console warning when the `item` prop is passed to the Grid2 component.
    *   Use the message: 'MUI Grid2: The `item` prop has been removed and is no longer necessary. You can safely remove it.'
    *   Ensure this warning is emitted only once per session.
*   Emit a console warning when the `zeroMinWidth` prop is passed to the Grid2 component.
    *   Use the message: 'MUI Grid2: The `zeroMinWidth` prop has been removed and is no longer necessary. You can safely remove it.'
    *   Ensure this warning is emitted only once per session.
*   Emit a console warning for any theme breakpoint prop (e.g., xs, sm, md, lg, xl) passed to the Grid2 component.
    *   Use the message pattern: 'MUI Grid2: The `{breakpoint}` prop has been removed. See https://mui.com/material-ui/migration/upgrade-to-grid-v2/ for migration instructions.'
    *   Replace `{breakpoint}` with the actual prop name.
    *   Ensure each breakpoint warning is emitted only once per session.
*   Update the `replaceMaterialLinks` utility to handle URL transformations.
    *   Map the path `/migration/upgrade-to-grid-v2/` to `/material-ui/migration/upgrade-to-grid-v2/`.
    *   Ensure the old path `/migration/migration-grid-v2/` is no longer used.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.