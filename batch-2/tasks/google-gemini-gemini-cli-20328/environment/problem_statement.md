## Description

The search tools in this project accept an optional parameter to filter which files are included in a search by specifying a file glob pattern. However, this parameter is currently named differently from its counterpart that excludes files from search, which is already consistently named with a descriptive suffix indicating it accepts a pattern. This naming inconsistency makes the API less predictable and harder to use.

## Expected Behavior

- The file inclusion filter parameter should be renamed to include the same descriptive pattern suffix used by the file exclusion filter parameter.
- After the rename, when the inclusion filter is specified with the new name, searches must correctly restrict results to only files matching the provided glob pattern (e.g., only files of a given extension type, or files matching multiple extension types).
- The human-readable description generated for a search invocation must display the file filter when the inclusion pattern is specified (e.g., showing the search query followed by the glob pattern being applied).
- When both an inclusion pattern and a directory path are specified, both should appear in the generated description.

## Why This Matters

Consistent parameter naming across the search tools makes the API more intuitive and self-documenting. Developers who discover one filter parameter will naturally expect the other to follow the same naming convention. The current inconsistency causes confusion and can lead to silent failures where a correctly-intended filter is silently ignored because the field name does not match what the implementation expects.
