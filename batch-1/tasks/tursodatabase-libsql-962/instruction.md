Update the Go module dependency files to include the missing concurrency utility package so that the test suite compiles successfully. Ensure that all existing tests pass and new tests compile and are skipped when appropriate.

*   Modify the `bindings/go/go.mod` file:
    *   Add `golang.org/x/sync v0.6.0` to the `require` block to resolve the import of "golang.org/x/sync/errgroup".
*   Update the `bindings/go/go.sum` file:
    *   Include the correct hash entries for `golang.org/x/sync v0.6.0` to allow the Go toolchain to verify the module.
*   Ensure all pre-existing local tests compile and pass:
    *   Tests include: exec, query, query-with-empty-result, exec-with-query, error-exec, error-query, error-rows-next, and error-non-utf8-url.
*   Verify new tests compile and are skipped when no remote database is available:
    *   Tests include: health-check ping, data type scanning, and concurrent single-connection queries.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.