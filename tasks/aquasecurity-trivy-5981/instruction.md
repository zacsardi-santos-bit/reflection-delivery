Update the Trivy CycloneDX SBOM output to use the modern component-based structure for tool metadata. Ensure backward compatibility with previously generated SBOMs using the deprecated format.

*   Modify the `Metadata` method in `pkg/sbom/cyclonedx/core/cyclonedx.go`:
    *   Change the return type to use `*cdx.ToolsChoice` with a `Components` sub-field containing a list of `cdx.Component` entries.
    *   Ensure the component entry has:
        *   `Type` set to `cdx.ComponentTypeApplication`
        *   `Name` set to "trivy"
        *   `Group` set to "aquasecurity"
        *   `Version` set to the application version string.
    *   Apply this structure across all scan scenarios: container image, local container, filesystem, multi-package, aggregate results, and empty result sets.

*   Update the CycloneDX Go library dependency:
    *   Ensure it is at least version v0.8.0 to support the `ToolsChoice` type.

*   Modify the `IsTrivySBOM` function in `pkg/sbom/cyclonedx/core/cyclonedx.go`:
    *   Update to check both the new `ToolsChoice.Components` format and the legacy `ToolsChoice.Tools` format.
    *   For the new format, match on `Group` and `Name`.
    *   For the legacy format, match on `Vendor` and `Name`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.