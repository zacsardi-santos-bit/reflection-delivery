Implement CycloneDX SBOM output support for the osv-scanner tool by adding new output format options for CycloneDX versions 1.4 and 1.5. Develop utility functions to parse ecosystem-specific package identifiers and a function to group packages from multiple sources. Ensure the CLI handles new format flags and exits with appropriate codes based on vulnerability findings.

*   Implement `FromComposer` in `internal/utility/purl/composer.go`:
    *   Accept a `PackageInfo` with a Name in 'vendor/package' format.
    *   Return vendor as namespace and package as name.
    *   Return a non-nil error for names with incorrect format or empty names.

*   Implement `FromGo` in `internal/utility/purl/golang.go`:
    *   Accept a `PackageInfo` with a Go module path.
    *   Return namespace (all segments except last) and name (last segment).
    *   Return a non-nil error for empty names.

*   Implement `FromMaven` in `internal/utility/purl/maven.go`:
    *   Accept a `PackageInfo` with a Name in 'groupId:artifactId' format.
    *   Return groupId as namespace and artifactId as name.
    *   Return a non-nil error for names with incorrect format or empty names.

*   Implement `Group` in `internal/utility/purl/package_grouper.go`:
    *   Accept a slice of `PackageSource` values.
    *   Return a map keyed by PURL string to deduplicated `PackageVulns`.
    *   Append `DepGroups` from all occurrences in merged entries.
    *   Return errors for packages that cannot be converted to a PURL.
    *   Use PURL format 'pkg:maven/NAMESPACE/NAME@VERSION' for Maven packages.

*   Define `CycloneDXVersion` in `pkg/reporter/sbom/models.go`:
    *   Create constants `CycloneDXVersion14` and `CycloneDXVersion15`.

*   Implement `NewCycloneDXReporter` in `pkg/reporter/cyclonedx.go`:
    *   Accept stdout and stderr writers, a `CycloneDXVersion`, and a `VerbosityLevel`.
    *   Return a `*CycloneDXReporter` implementing the `Reporter` interface.
    *   Write log messages to stderr based on verbosity level.

*   Update `pkg/reporter/format.go`:
    *   Include "cyclonedx-1-4" and "cyclonedx-1-5" in the supported format list.
    *   Modify `New()` to create `CycloneDXReporter` for these formats.

*   Update CLI to support `--format cyclonedx-1-4` and `--format cyclonedx-1-5` flags:
    *   Exit with code 0 when no vulnerabilities are found.
    *   Exit with code 1 when vulnerabilities are present.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.