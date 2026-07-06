Create a dedicated mock widget for testing purposes, separate from any production widget. Ensure it integrates with standard renderer APIs and supports generating AI prompt JSON output. Update existing test fixtures and data to use this mock widget instead of the production input widget.

*   Implement the `getPromptJSON` function in `packages/perseus/src/widget-ai-utils/mock-widget/prompt-utils.ts`:
    *   Accept `renderProps` with a `value` field and `PerseusMockWidgetUserInput` with a `currentValue` field.
    *   Return an object: `{ type: 'mock-widget', options: { value: renderProps.value }, userInput: { value: userInput.currentValue } }`.

*   Define types in `packages/perseus/src/validation.types.ts`:
    *   `PerseusMockWidgetUserInput`: `{ currentValue: string }`.
        *   Add to the `UserInput` union type.
    *   `PerseusMockWidgetRubric`: `{ value: string }`.
        *   Add to the `Rubric` union type.

*   Update `packages/perseus-core/src/data-schema.ts`:
    *   Add `MockWidget` and `MockWidgetOptions` types.
        *   `MockWidget = WidgetOptions<'mock-widget', MockWidgetOptions>`.
        *   `MockWidgetOptions = { static?: boolean; value: string }`.
    *   Include `MockWidget` in `PerseusWidgetTypes` interface and `PerseusWidget` union type.

*   Implement and export the `MockWidget` component:
    *   Export as a named export from `packages/perseus/src/widgets/mock-widgets/index.ts`.
    *   Export as the default from `packages/perseus/src/widgets/mock-widgets/mock-widget.tsx`.
    *   Use Wonder Blocks `TextField` and `View` for rendering.
    *   Implement methods: `getUserInput()`, `static getUserInputFromProps(props)`, `setInputValue(path, value, callback)`, `getInputPaths()`, `focus()`, `blur()`, `serialize()`, `restoreSerializedState()`, `getPromptJSON()`.

*   Relocate and update the `mock-asset-loading` widget:
    *   Move from `packages/perseus/src/__tests__/mock-asset-loading-widget.tsx` to `packages/perseus/src/widgets/mock-widgets/mock-asset-loading-widget.tsx`.
    *   Update relative import paths.
    *   Export `MockAssetLoadingWidget`, `mockedAssetItem`, and the default widget.

*   Update test data in `packages/perseus/src/__testdata__/server-item-renderer.testdata.ts`:
    *   Export `itemWithMockWidget`, `itemWithTwoMockWidgets`, and rename `itemWithInput` to `itemWithNumericInput`.

*   Modify `packages/perseus/src/__testdata__/renderer.testdata.ts`:
    *   Replace `inputNumberWidget` with `mockWidget`.
    *   Update `question2` to use `mock-widget`.

*   Adjust `widgets.test.ts` to target `numeric-input` for `getUserInputFromProps`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.