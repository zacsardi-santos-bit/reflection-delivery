Implement a "view" system in the metrics SDK to allow users to customize how instruments are matched and transformed before data export. Enable users to match instruments by name with wildcard support, rename instruments, update descriptions, and filter attributes. Ensure error handling for invalid configurations.

*   Implement the `New` function in `sdk/metric/view/view.go`:
    *   Return an error and a zero-value `View` if no match options are provided.
    *   Return an error and a zero-value `View` if `WithRename` is used with a wildcard name pattern.

*   Implement `MatchInstrumentName` in `sdk/metric/view/view.go`:
    *   Compile the name into a regex pattern stored in `instrumentName` of type `*regexp.Regexp`.
    *   Set `hasWildcard` to true if the name contains `*` or `?`, otherwise set to false.
    *   Treat `*` as matching zero or more characters, `?` as matching exactly one character, and all other regex metacharacters as literals.

*   Implement `matchName(name string) bool` in the `View` struct:
    *   Return true if the instrument name matches the compiled `instrumentName` pattern.
    *   Return true unconditionally if no `instrumentName` is set.

*   Implement `TransformInstrument` in the `View` struct:
    *   Return `(Instrument{}, false)` if the instrument does not match the configured criteria.
    *   Return the transformed instrument and true if it matches.
    *   Set the instrument's `Name` field to the rename value if `WithRename` is configured.
    *   Set the instrument's `Description` field if `WithSetDescription` is configured.

*   Implement `MatchInstrumentationLibrary` in `sdk/metric/view/view.go`:
    *   Match instruments only on non-empty fields of the provided `instrumentation.Library`.

*   Implement `TransformAttributes` in the `View` struct:
    *   Return the original attribute set unchanged if no attribute filter is configured.
    *   Return a new attribute set containing only keys specified in `WithFilterAttributes`.

*   Define the `Instrument` struct in `sdk/metric/view/instrument.go`:
    *   Include exported fields: `Scope` of type `instrumentation.Library`, `Name` of type `string`, and `Description` of type `string`.

*   Define the `View` struct in `sdk/metric/view/view.go`:
    *   Include unexported fields: `instrumentName` of type `*regexp.Regexp` and `hasWildcard` of type `bool`.

*   Implement the `Option` interface in `sdk/metric/view/view.go`:
    *   Define the `apply(View) View` signature.

*   Implement the following functions in `sdk/metric/view/view.go`:
    *   `WithRename(name string) Option`: Disallow combination with wildcard patterns.
    *   `WithSetDescription(desc string) Option`: Set the instrument description.
    *   `WithFilterAttributes(keys ...attribute.Key) Option`: Filter attributes by specified keys.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.