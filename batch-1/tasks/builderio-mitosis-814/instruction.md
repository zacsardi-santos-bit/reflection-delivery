Implement the necessary changes to ensure that the React code generator correctly supports MobX as a state management strategy. When MobX is selected, ensure that the generated code includes all necessary imports and correctly wraps the component for reactivity.

*   Update the `componentToReact` function in `packages/core/src/generators/react/generator.ts`:
    *   When `stateType` is `'mobx'`:
        *   Import both `useLocalObservable` and `observer` from `'mobx-react-lite'`.
        *   Declare the component function as a named function without using `export default`.
        *   Initialize local component state using `useLocalObservable(() => ({ ... }))`.
        *   After the component function body, append:
            *   `const observed{ComponentName} = observer({ComponentName});`
            *   `export default observed{ComponentName};`
            *   Replace `{ComponentName}` with the actual component name, defaulting to `MyComponent` if unnamed.
    *   Ensure that when `stateType` is `'useState'` or any other type, the existing behavior and output remain unchanged.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.