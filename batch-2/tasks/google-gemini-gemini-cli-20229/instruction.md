Implement concurrency-safe extension loading in the `ExtensionManager` class to ensure all concurrent calls to `loadExtensions()` share the same operation and result. Detect and handle duplicate extension names across directories, and make the `loadExtension()` method publicly accessible to allow external calls.

*   Update the `ExtensionManager` class in `packages/cli/src/config/extension-manager.ts`:
    *   Implement `loadExtensions()` to be concurrency-safe:
        *   Ensure concurrent calls return the same in-flight Promise object.
        *   Resolve all concurrent calls to the identical array reference.
    *   After `loadExtensions()` resolves, subsequent calls must throw an error with the message: 'Extensions already loaded, only load extensions once.'
    *   If the user extensions directory is missing, `loadExtensions()` must return an empty array without throwing an error.
    *   Detect duplicate extension names across directories:
        *   If duplicates are found, throw an error with the message: 'Extension with name <name> already was loaded.' where `<name>` is the conflicting extension name.
    *   Ensure `loadExtension(extensionDir: string)` is publicly accessible:
        *   Allow it to be called externally to load a single extension from a specified directory.
    *   When `loadExtension()` is called during an in-progress `loadExtensions()` operation:
        *   Ensure `loadExtension()` waits for `loadExtensions()` to complete before proceeding.
    *   After both `loadExtensions()` and any concurrent `loadExtension()` calls complete:
        *   Ensure `getExtensions()` returns all extensions from both operations combined.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.