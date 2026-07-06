Implement a new hidden admin subcommand 'zstd' in the Dolt CLI to verify the integration of the Zstandard compression library. Ensure the command executes successfully without requiring a repository and update the Go module files to include the new dependency.

*   Update the Dolt CLI:
    *   Register a new subcommand 'zstd' under the 'dolt admin' command group in `go/cmd/dolt/commands/admin/admin.go`.
    *   Ensure 'dolt admin zstd' executes successfully with exit code 0 and does not require a repository (RequiresRepo returns false).

*   Create a new command implementation:
    *   Create `zstd.go` in `go/cmd/dolt/commands/admin/`.
    *   Implement the Dolt CLI command interface in the 'admin' package.
        *   Define a struct with:
            *   `Name()` method returning "zstd".
            *   `RequiresRepo()` method returning false.
            *   `Exec()` method returning 0 (success).
            *   `Hidden()` method returning true.
            *   `ArgParser()` method accepting zero arguments.
    *   Import and use the `github.com/dolthub/gozstd` package to ensure the compression library is linked.

*   Update Go module files:
    *   Modify `go/go.mod` to require `github.com/dolthub/gozstd v0.0.0-20240423170813-23a2903bca63`.
    *   Modify `go/go.sum` to include:
        *   `h1:OAsXLAPL4du6tfbBgK0xXHZkOlos63RdKYS3Sgw/dfI=` (h1 hash).
        *   `go.mod h1:lV7lUeuDhH5thVGDCKXbatwKy2KW80L4rMT46n+Y2/Q=` (go.mod hash).

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.