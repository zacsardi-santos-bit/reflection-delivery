Update the `test_storage` function to ensure that the temporary directory remains accessible for the duration of the test by returning a tuple containing both the directory guard and the storage instance. This will allow callers to manage the directory's lifetime.

*   Modify the `test_storage` function in `rust/storage/src/lib.rs`:
    *   Change the return type to a 2-tuple: `(TempDir, Storage)`.
    *   Ensure the function returns the `TempDir` guard from the `tempfile` crate alongside the `Storage` instance.
    *   Ensure the temporary directory remains on disk and accessible immediately after the function returns.
    *   Ensure the `TempDir` returned is the owning guard, allowing the caller to control the directory's cleanup by managing the `TempDir`'s scope.

*   Ensure compatibility:
    *   Verify that all existing tests in the `chroma-storage` package compile and pass after the change.
    *   Update any test calls to `test_storage` to handle the new return type, ensuring they unpack the returned pair and maintain the `TempDir` guard as needed.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.