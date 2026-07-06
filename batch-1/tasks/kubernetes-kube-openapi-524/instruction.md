Migrate your project from the archived random data generation library to the actively-maintained successor. Update module dependencies and any affected non-test source files to ensure compatibility with the new library's API.

*   Update module dependencies:
    *   In the root module's `go.mod`, add `sigs.k8s.io/randfill` (v1.0.0 or compatible) and remove `github.com/google/gofuzz`.
    *   Update the root module's `go.sum` to include checksums for `sigs.k8s.io/randfill` and remove those for `github.com/google/gofuzz`.
    *   In the `test/integration` module's `go.mod`, remove `github.com/google/gofuzz` and add `sigs.k8s.io/randfill` v1.0.0 as an indirect dependency.
    *   Update the `test/integration` module's `go.sum` accordingly.

*   Modify non-test Go source files:
    *   Replace imports of `github.com/google/gofuzz` with `sigs.k8s.io/randfill`.
    *   Update API calls:
        *   Replace `Fuzz`/`FuzzNoCustom` with `Fill`/`FillNoCustom`.
        *   Replace `RandBool` with `Bool`.
        *   Replace `RandString` with `String(0)`.
        *   Replace `RandUint64` with `Uint64`.
        *   Replace `fuzz.Continue` type with `randfill.Continue`.

*   Update the `OpenAPIV3FuzzFuncs` variable:
    *   Ensure it uses `randfill.Continue` in callback function signatures.
    *   Ensure compatibility with `randfill.Filler.Funcs(...)`.

*   Ensure all callback functions registered with the fuzzer accept `randfill.Continue` as their second argument.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.