Implement a lazy loading mechanism for a template language server that dynamically loads and unloads template files based on their dependency graph. Ensure that files are opened and closed in the correct order and handle circular dependencies without infinite loops.

*   Define the `templDocLazyLoader` struct with the following fields:
    *   `templDocHooks` of type `templDocHooks`.
    *   `packageLoader` of type `packageLoader` interface.
    *   `fileReader` of type `fileReader` interface.
    *   `docsOpenCount` as a map of string to int.

*   Define the `templDocHooks` struct with function fields:
    *   `didOpen` with signature `func(ctx context.Context, params *lsp.DidOpenTextDocumentParams) error`.
    *   `didClose` with signature `func(ctx context.Context, params *lsp.DidCloseTextDocumentParams) error`.

*   Define interfaces:
    *   `packageLoader` with method `load(file string) ([]*packages.Package, error)`.
    *   `fileReader` with method `read(file string) ([]byte, error)`.

*   Implement `newTemplDocLazyLoader` function:
    *   Accept a `templDocHooks` argument.
    *   Return a `templDocLazyLoader` with `templDocHooks.didOpen` and `templDocHooks.didClose` set from the argument.
    *   Initialize `docsOpenCount` as a non-nil map.

*   Implement the `load` method for `templDocLazyLoader`:
    *   Resolve the file path from `params` URI.
    *   Call `packageLoader.load` with the resolved path.
    *   Return error: `load packages for file "<path>": <underlying error>` if loading fails.
    *   Traverse the package dependency graph in topological order.
    *   For each `.templ` file in `OtherFiles`:
        *   Increment `docsOpenCount` unconditionally.
        *   Call `fileReader.read` and `templDocHooks.didOpen` only if `docsOpenCount` was 0.
        *   Handle errors with specific formats and ensure `docsOpenCount` is not incremented on failure.
    *   Handle circular imports by skipping already-visited package paths.

*   Implement the `unload` method for `templDocLazyLoader`:
    *   Resolve the file path from `params` URI.
    *   Call `packageLoader.load` with the resolved path.
    *   Return error: `load packages for file "<path>": <underlying error>` if loading fails.
    *   Traverse the package dependency graph in reverse topological order.
    *   For each `.templ` file:
        *   If `docsOpenCount > 1`, decrement it without calling `templDocHooks.didClose`.
        *   If `docsOpenCount == 1`, call `templDocHooks.didClose` and remove the file from `docsOpenCount`.
        *   Handle errors with specific formats and ensure `docsOpenCount` is not modified on failure.
    *   Handle circular imports by skipping already-visited package paths.

*   Ensure only `.templ` files from a package's `OtherFiles` list are processed by `load` and `unload`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.