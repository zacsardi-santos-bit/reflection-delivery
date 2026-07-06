Update the bundle version label in the codebase to reflect the release candidate milestone. Ensure that the conformance tooling correctly identifies and returns the updated version when checking CRD annotations.

*   Modify the version label:
    *   Update the `BundleVersion` constant in `pkg/consts/consts.go`:
        *   Change the value from `"v1.2.0-dev"` to `"v1.2.0-rc1"`.

*   Ensure version detection logic:
    *   Confirm that the logic reading bundle-version annotations from installed Gateway API CRDs returns `"v1.2.0-rc1"` when:
        *   All CRDs are annotated with `"v1.2.0-rc1"`.
        *   A consistent channel, such as `"standard"`, is used.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.