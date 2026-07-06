Update the project's Go module files to resolve compilation issues by upgrading the cryptography library and its transitive dependencies to compatible versions. Ensure these updates are consistently applied across all relevant files in the repository.

*   Update the root `go.mod` file:
    *   Set `golang.org/x/crypto` to version `v0.17.0`.
    *   Set `golang.org/x/sys` to version `v0.15.0`.
    *   Set `golang.org/x/term` to version `v0.15.0`.
    *   Set `golang.org/x/text` to version `v0.14.0`.

*   Update the root `go.sum` file:
    *   Include the correct cryptographic checksums for `golang.org/x/crypto v0.17.0`, `golang.org/x/sys v0.15.0`, `golang.org/x/term v0.15.0`, and `golang.org/x/text v0.14.0`.

*   Update all submodule `go.mod` files (e.g., `cmd/acmesolver`, `cmd/cainjector`):
    *   Ensure they declare the same updated versions for `golang.org/x/crypto`, `golang.org/x/sys`, `golang.org/x/term`, and `golang.org/x/text`.

*   Update all submodule `go.sum` files:
    *   Ensure checksums match the new dependency versions.

*   Update all `LICENSES` files:
    *   Reference the new version URLs for `golang.org/x/crypto`, `golang.org/x/sys`, `golang.org/x/term`, and `golang.org/x/text`.

*   Verify that the project compiles successfully after updates.
*   Ensure the RFC2136 DNS01 integration tests pass post-update.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.