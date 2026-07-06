Upgrade the Go ADBC drivers to use the next major version of the Arrow Go library and fix the FlightSQL driver's cookie handling bug. Ensure that all relevant import paths and dependency files are updated, and implement the necessary logic to propagate session cookies and reuse authentication tokens for sub-connections.

*   Update Arrow Go library version:
    *   Change all import paths from `github.com/apache/arrow/go/v15/...` to `github.com/apache/arrow/go/v16/...` in all Go source files.
    *   Modify `go.mod` to reference Arrow Go v16 at version `v16.0.0-20240129203910-c2ca9bcedeb0`.
    *   Update `go.sum` to include the corresponding checksums for the new Arrow Go version.
    *   Ensure the FlightSQL driver reports the vendor arrow version as `16.0.0-SNAPSHOT` for the `InfoVendorArrowVersion` info code.

*   Fix FlightSQL driver's cookie handling:
    *   When a FlightSQL server returns flight endpoints with non-empty location URIs, ensure the driver creates a sub-client connecting to that location.
    *   Clone the cookie middleware state from the primary connection and apply it to each sub-client to maintain cookie propagation across sub-connections.
    *   Reuse existing authentication tokens for sub-clients. Only perform basic token authentication if no authentication token is available.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.