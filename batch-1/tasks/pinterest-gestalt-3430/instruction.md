Implement a shared testing utility to render components with a specified feature flag enabled, allowing tests to exercise the new popover implementation. Update dropdown components to support test identifiers and adjust snapshot tests accordingly.

*   Create a function `renderWithExperiment` in `packages/gestalt/src/utils/testing/renderWithExperiment.js`.
    *   Accepts two arguments: `experiment` (string) and `children` (React element).
    *   Returns the result of `@testing-library/react`'s `render()` including a `container` property.
    *   Wraps the provided React element in `ExperimentProvider` with the experiment enabled.
    *   Use the provider value `{ [experiment]: { 'anyEnabled': true, 'group': 'enabled' } }`.
    *   Import `ExperimentProvider` from `../../contexts/ExperimentProvider`.
    *   Ensure the function supports the following experiment names:
        *   `'web_gestalt_popover_v2_combobox'`
        *   `'web_gestalt_popover_v2_dropdown'`
        *   `'web_gestalt_popover_v2_helpbutton'`
        *   `'web_gestalt_popover_v2_confirmationpopover'`
        *   `'web_gestalt_popover_v2_popovereducational'`
        *   `'web_gestalt_tooltip_v2'`
    *   Use the Flow type signature: `renderWithExperiment(experiment: string, children: React$Element<React$ElementType>): ReturnType<typeof render>`.

*   Update `Dropdown.Item` and `Dropdown.Link` components to support a `dataTestId` prop.
    *   Add an optional `dataTestId` prop (string) to map to a `data-testid` attribute on the rendered DOM element.
    *   Enable `screen.getByTestId(dataTestId)` queries for keyboard navigation tests.

*   Manage snapshot files for popover-based components.
    *   Delete or regenerate snapshots for:
        *   `packages/gestalt/src/__snapshots__/Popover.jsdom.test.js.snap`
        *   `packages/gestalt/src/__snapshots__/PopoverEducational.jsdom.test.js.snap`
    *   Ensure snapshots capture the new DOM-based output.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.