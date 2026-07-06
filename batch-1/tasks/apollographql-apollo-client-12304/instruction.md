Implement changes to the Apollo Client cache to ensure that incomplete or missing data queries return clear indicators of "no data" rather than ambiguous empty objects. Modify the handling of missing fields to avoid throwing exceptions and provide structured error information.

*   Update the `DiffResult` type in `src/cache/core/types/DataProxy.ts`:
    *   Ensure the `result` field is `null` when the query is incomplete and no data is available.
    *   Ensure the `missing` field is a single `MissingFieldError` instance, not an array.

*   Modify the `MissingFieldError` class in `src/cache/core/types/common.ts`:
    *   Ensure it is exported from `src/cache/index.ts` for public access.

*   Adjust the `diffQueryAgainstStore` method in `src/cache/inmemory/readFromStore.ts`:
    *   Ensure it returns `{ complete: false, result: null, missing: MissingFieldError }` when no fields can be resolved.
    *   Ensure it does not throw errors when fields are missing.
    *   Ensure the `missing` property is a single `MissingFieldError`.

*   Update the `readQueryFromStore` function in `src/cache/inmemory/readFromStore.ts`:
    *   Ensure it returns `null` when fields are missing, instead of throwing an error.

*   Modify observable query results:
    *   Ensure the `data` field is `undefined` when cache data is incomplete or missing, rather than an empty object.

*   Implement a custom Jest equality tester in `src/config/jest/areMissingFieldErrorsEqual.ts`:
    *   Compare `MissingFieldError` instances by `message`, `path`, `query`, `variables`, and `missing`.
    *   Register this tester in `src/config/jest/setup.ts`.
    *   Ensure it returns `true` for equal `MissingFieldError` instances, `false` when only one side is a `MissingFieldError`, and `undefined` when neither side is a `MissingFieldError`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.