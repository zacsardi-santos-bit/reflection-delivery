Implement a smarter comparison function to accurately identify new vulnerabilities in CI pipelines, ignoring file path changes. This function should count vulnerability occurrences by ID across entire scans and report only genuine new vulnerabilities.

* Implement `DiffVulnerabilityResultsByOccurrences` in `internal/ci/vulnerability_result_diff.go` with the signature:
  ```go
  DiffVulnerabilityResultsByOccurrences(oldRes, newRes models.VulnerabilityResults) map[string]int
  ```
  * Count occurrences of each vulnerability by ID across all sources in both `oldRes` and `newRes`.
  * Return a map of vulnerability IDs where `newRes` has more occurrences than `oldRes`, with the excess count as the map value.
  * Return an empty map when both inputs are identical or when IDs are the same but under different source paths.
  * Include IDs in the result only if they appear more times in `newRes` than in `oldRes`.

* Create fixture files in `internal/ci/fixtures/vulns/`:
  * `test-vuln-unique-diff-a-a.json`, `test-vuln-unique-diff-a-a-1.json`, `test-vuln-unique-diff-b-a.json` — each containing an empty JSON object: `{}`.
  * `test-vuln-unique-diff-a-b.json` with content `{"GHSA-c3h9-896r-86jm": 1}` to represent one net-new occurrence.
  * `test-vuln-results-a-1.json` as a variant of `test-vuln-results-a.json` with a changed Go vulnerability source path, keeping IDs identical.
  * `test-vuln-diff-a-a.json` containing `{"results": []}` for identical old and new inputs.
  * `test-vuln-diff-a-a-1.json` with full `VulnerabilityResults` JSON output for moved-path results.

* Update `internal/ci/fixtures/vulns/test-vuln-results-a.json`:
  * Correct the group ID for the Go vulnerability from `GHSA-c3h9-896r-86jm` to `GO-2021-0053`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.