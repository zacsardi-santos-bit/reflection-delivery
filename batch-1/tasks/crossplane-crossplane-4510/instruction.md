Implement a nil check for schema validation in the `ForCompositeResource` function within the `internal/xcrd` package. Ensure that when schema validation is missing, the system returns a clear, descriptive error message identifying the affected resource definition by name and type.

*   Update the `ForCompositeResource` function in `internal/xcrd/crd.go`:
    *   Return `(nil, error)` if a version's `CompositeResourceValidation` is nil.
    *   Format the error using `errors.Wrap(errors.New(errCustomResourceValidationNil), fmt.Sprintf(errFmtGenCrd, "Composite Resource", d.GetName()))`.

*   Define a new unexported package-level string constant:
    *   Name: `errCustomResourceValidationNil`
    *   Location: `internal/xcrd/crd.go`
    *   Value: `'custom resource validation cannot be nil'`

*   Ensure the existing format string constant remains unchanged:
    *   Name: `errFmtGenCrd`
    *   Location: `internal/xcrd/crd.go`
    *   Value: `'cannot generate CRD for %q %q'`

*   Ensure that when `CompositeResourceValidation` is non-nil, including an empty schema object, `ForCompositeResource` returns a valid `CustomResourceDefinition` without error.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.