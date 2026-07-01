Refactor the internal tooling scripts to use the standard Go testing framework. Expose the core logic of each tool through exported functions in standalone modules and ensure they can be tested with a single command using a Go workspace configuration.

*   Update the `tools/format_self` package:
    *   Declare it as a standalone Go module with `module github.com/git-town/git-town/tools/format_self` and `go 1.21.4` in `go.mod`.
    *   Export the functions `FormatFileContent`, `FormatLine`, and `IsGoFile` in `tools/format_self/format_self.go`.
        *   `FormatLine(line string) string`: Rename the receiver identifier to 'self' in method signature lines starting with 'func ('. Leave other lines unchanged.
        *   `FormatFileContent(content string) string`: Apply `FormatLine` to each line of the provided content and return the result. Return unchanged content if all receivers are already 'self'.
        *   `IsGoFile(path string) bool`: Return true for '.go' files not ending in '_test.go'. Return false otherwise.

*   Update the `tools/format_unittests` package:
    *   Ensure the package exports `IsTopLevelRunLine`, `FormatFileContent`, and `IsGoTestFile` as defined in `tools/format_unittests/format_unittests.go`.
        *   `IsTopLevelRunLine(line string) bool`: Return true for lines starting with one tab and 't.Run("' and ending with ', func(t *testing.T) {'. Return false for nested subtests.
        *   `FormatFileContent(content string) string`: Ensure a blank line precedes each top-level subtest run call. Return unchanged content if already correctly formatted or without subtests.
        *   `IsGoTestFile(path string) bool`: Return true for paths ending in '_test.go'. Return false otherwise.

*   Update the `tools/structs_sorted` package:
    *   Declare it as a standalone Go module with `module github.com/git-town/git-town/tools/structs_sorted` and `go 1.21.4` in `go.mod`.
    *   Export the `Issues` type and `LintFile` function in `tools/structs_sorted/structs_sorted.go`.
        *   `Issues` type: A named slice with a `String() string` method that formats issues.
        *   `LintFile(path string) Issues`: Parse the Go file at the given path, checking for unsorted struct fields. Return formatted issues or an empty `Issues` for sorted or exempt structs.

*   Create a `go.work` file at the repository root:
    *   Reference `.` (repo root), `./tools/format_self`, `./tools/format_unittests`, and `./tools/structs_sorted`.
    *   Use `go 1.21.4` to enable testing all tools with `go test ./tools/format_self/... ./tools/format_unittests/... ./tools/structs_sorted/...`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.