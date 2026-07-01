Refactor the mock generator constructor to use a configuration struct instead of positional arguments. Implement the struct with fields for source directory, package name, and formatter choice, ensuring backward compatibility and sensible defaults.

*   Update the `New` function in the `moq` package to accept a single `Config` struct argument.
    *   Signature: `New(conf Config) (*Mocker, error)`
*   Define the `Config` struct in `pkg/moq/moq.go` with the following fields:
    *   `SrcDir` (string): Required field for the source directory path.
    *   `PkgName` (string): Optional field for the package name of the generated mock. Defaults to the source package name if empty.
    *   `Formatter` (string): Optional field for the code formatter. Defaults to "gofmt" if empty; set to "goimports" to use goimports.
*   Ensure the `New` function behaves as follows:
    *   If only `SrcDir` is set, infer the package name from the source package.
    *   If both `SrcDir` and `PkgName` are set, use the provided `PkgName`.
    *   If `Formatter` is set to "goimports", use goimports for formatting.
    *   If `Formatter` is empty or not set, default to using gofmt.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.