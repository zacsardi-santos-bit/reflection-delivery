Add support for Starlark configuration to the CI/CD pipeline system. Implement the conversion from Starlark data types to JSON, ensuring correct handling of all value types and error conditions. Update the configuration parsing logic to recognize and process Starlark scripts.

*   Implement the `starlarkJSON` function in `internal/config/starlark.go`:
    *   Accept a `bytes.Buffer` and a `starlark.Value`.
    *   Write the JSON representation to the buffer.
    *   Handle `starlark.NoneType` (output 'null'), `starlark.Bool`, `starlark.Int`, `starlark.Float`, `starlark.String`, `starlark.Indexable` (lists/tuples as JSON arrays), and `*starlark.Dict` (as JSON objects).
    *   Return an error with the message 'cannot convert non-string dict key to JSON' if a `*starlark.Dict` has a non-string key.
    *   Properly JSON-escape `starlark.String` values with special characters, without HTML escaping.

*   Add the `ConfigFormatStarlark` constant in `internal/config/config.go`:
    *   Define it as the third value in the `ConfigFormat` iota after `ConfigFormatJSON` and `ConfigFormatJsonnet`.

*   Update the `ParseConfig` function in `internal/config/config.go`:
    *   Handle the `ConfigFormatStarlark` format.
    *   Execute the Starlark script using `execStarlark`.
    *   Convert the result to JSON before parsing.
    *   Return an error with the prefix 'failed to execute starlark: ' if execution fails.

*   Implement the `execStarlark` function in `internal/config/starlark.go`:
    *   Execute a Starlark script from `configData`.
    *   Call the script's 'main' function with a context dictionary containing 'ref_type', 'ref', 'branch', 'tag', 'pull_request_id', and 'commit_sha'.
    *   Convert the returned dictionary to JSON bytes.

*   Ensure the system detects and processes Starlark scripts:
    *   Store Starlark configuration files at `.agola/config.star`.
    *   Recognize files with the `.star` extension as Starlark format.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.