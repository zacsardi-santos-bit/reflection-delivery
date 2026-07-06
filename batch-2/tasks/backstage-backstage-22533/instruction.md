Implement the necessary changes to ensure the Backstage frontend framework resolves component references by their unique identifier string rather than object identity. Update the components API to initialize directly from the application's extension tree and make certain utility function parameters optional.

*   Modify the `DefaultComponentsApi` class:
    *   Implement a static factory method `fromTree(tree: AppTree): DefaultComponentsApi` that initializes the API using the app tree's component extension attachments.
    *   Ensure `fromTree` traverses the tree's root node component attachments, extracting each component's reference and implementation using `createComponentExtension.componentDataRef`, and builds an internal map keyed by the reference's ID string.
    *   Update the `getComponent<T extends {}>(ref: ComponentRef<T>): ComponentType<T>` method to look up components by the reference's ID string, ensuring consistent resolution for distinct reference objects with the same ID.
    *   Ensure components returned by `getComponent` are valid React component types that render correctly as JSX elements.
    *   Export the `DefaultComponentsApi` class from `packages/frontend-app-api/src/apis/implementations/ComponentsApi/DefaultComponentsApi.ts`.

*   Update the `resolveAppNodeSpecs` function:
    *   Accept `features`, `builtinExtensions`, and `parameters` as optional fields, defaulting to empty arrays.
    *   Ensure callers can pass only the `features` option without encountering type errors or runtime failures.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.