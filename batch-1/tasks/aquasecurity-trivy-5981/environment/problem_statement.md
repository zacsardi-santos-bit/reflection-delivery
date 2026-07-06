## Description

Trivy's CycloneDX SBOM output uses a deprecated format for recording tool metadata in the BOM's metadata section. The current code represents the scanner as an entry in a simple tool list, which was the format used by an older version of the CycloneDX specification. The specification has since been updated to represent tools as full software components, which allows richer metadata and is the format expected by modern CycloneDX consumers and validators.

## Expected Behavior

- When Trivy generates a CycloneDX SBOM, the tool metadata in the BOM's metadata section should use the current component-based structure rather than the legacy tool array format.
- The scanner should be represented as an application-type component with its vendor recorded in the group field and its name and version included as component attributes.
- This should apply consistently across all scan output types (container images, filesystems, aggregated results, etc.).
- For backward compatibility, Trivy should still recognize CycloneDX SBOMs that were previously produced using the old tool array format.

## Why This Matters

CycloneDX has deprecated the old tool array format in favor of component-based tool entries. Trivy's output currently fails to conform to the current specification, which can cause issues with tools and validators that expect the modern format. Updating the output ensures Trivy-generated SBOMs are compatible with the ecosystem of tools that have adopted the newer CycloneDX specification.
