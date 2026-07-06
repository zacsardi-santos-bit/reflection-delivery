## Description

The eval inventory tooling currently only produces a human-readable text report when scanning evaluation test cases in the repository. There is no structured, machine-readable output format, making it difficult for automated tooling, dashboards, or CI pipelines to consume and process inventory data programmatically.

## Expected Behavior

- A new JSON output mode should be available for the eval inventory feature, producing a structured, versioned, and deterministic representation of the inventory.
- The JSON output should include a format version marker, a generation timestamp, a summary with file and case counts broken down by policy, full case details, and diagnostic information.
- All file paths in the JSON output should be relative rather than absolute so the output is portable across different environments.
- The generation timestamp should be overridable via environment variables to support reproducible builds where deterministic output is required.
- Cases with any policy value (including unrecognized or future policies) should appear in both the text report and the JSON output.
- When the expected evaluation directory does not exist under the given repository root, inventory collection should fail with a clear, descriptive error message instead of silently returning empty results.
- The repository root should be tracked in the inventory result so that relative paths can be computed correctly throughout the tool.

## Why This Matters

Without a structured JSON format, integrating eval inventory data into automated workflows requires fragile text parsing. A deterministic, versioned JSON output makes it straightforward to diff inventory changes across commits, feed data into dashboards, and verify inventory contents in automated checks. The improved error message for missing evaluation directories also helps developers quickly diagnose misconfigured repository paths.
