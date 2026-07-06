Migrate the classification page component to a standard JavaScript module format. Ensure it exports the component as the default export and preserves the correct rendering logic.

*   Implement the ProjectClassifyPage component as a default export in the module located at `app/pages/project/classify`.
    *   Ensure it is importable using the default export syntax from the specified path.
*   Ensure the ProjectClassifyPage component renders correctly:
    *   When rendered with a `project` prop and no logged-in user, it must render a root container element with the CSS class 'classify-page'.
    *   The FinishedBanner component should only be rendered when the `projectIsComplete` prop is truthy.
        *   Ensure the FinishedBanner does not appear if `projectIsComplete` is absent or falsy.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.