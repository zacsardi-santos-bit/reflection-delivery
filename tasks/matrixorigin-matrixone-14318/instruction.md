Upgrade the UUID library in your Go codebase and replace deprecated UUID generation functions with the modern time-ordered UUID format to resolve compilation issues and prevent MAC address embedding.

*   Update the UUID library in `go.mod` to version 1.4.0 or later of `github.com/google/uuid`.
    *   Ensure `go.sum` is updated accordingly.
*   Replace all instances of `uuid.NewUUID()` with `uuid.NewV7()` throughout the codebase.
*   Replace all instances of `uuid.New()` and `uuid.NewString()` used for ID generation with `uuid.NewV7()`.
    *   This includes usage in service configuration, pipeline construction, session setup, and computation wrappers.
*   Update test infrastructure files to use `uuid.NewV7()` for generating UUIDs for CN service, TN service, log service, and test cluster configurations.
*   Ensure generated v7 UUIDs do not have node bytes matching Docker MAC address prefixes.
*   Verify that all packages previously failing due to the removed `uuid.NewUUID()` function now compile successfully and pass their tests.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.