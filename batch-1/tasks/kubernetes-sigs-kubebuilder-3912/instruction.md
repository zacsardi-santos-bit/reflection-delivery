Update the Go v4 scaffolding plugin to maintain a single, authoritative version identifier for the controller-runtime library it targets. Ensure this identifier is accessible within the scaffolding package and reflects the current intended version.

*   Define a package-level identifier named `ControllerRuntimeVersion` in the `scaffolds` package located at `pkg/plugins/golang/v4/scaffolds/`.
    *   Set the value of `ControllerRuntimeVersion` to the string "v0.18.2".
    *   Use the signature: `ControllerRuntimeVersion string = "v0.18.2"`.
    *   Ensure the identifier is declared as a constant or variable and is accessible within the package.
*   If `ControllerRuntimeVersion` already exists in any file within the `scaffolds` package, update its value to "v0.18.2" instead of creating a new declaration.
    *   Avoid duplicate package-level identifiers, as Go does not permit them.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.