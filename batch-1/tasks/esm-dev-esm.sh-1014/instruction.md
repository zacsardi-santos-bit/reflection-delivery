Implement a custom encoding and decoding system for build metadata in the server package. Replace the current JSON-based storage with a more compact byte representation to improve efficiency. Ensure that the encoding and decoding processes maintain data integrity and handle both populated and zero-value metadata records correctly.

*   Define the `BuildMeta` struct in `server/build_meta.go` with the following fields:
    *   `CJS` (bool)
    *   `HasCSS` (bool)
    *   `TypesOnly` (bool)
    *   `ExportDefault` (bool)
    *   `Dts` (string)
    *   `Imports` ([]string)

*   Implement the `encodeBuildMeta` function in `server/build_meta.go`:
    *   Signature: `encodeBuildMeta(meta *BuildMeta) []byte`
    *   Encode a `BuildMeta` struct into a compact byte slice.
    *   Ensure the returned byte slice is non-empty when the `BuildMeta` has any non-zero fields.

*   Implement the `decodeBuildMeta` function in `server/build_meta.go`:
    *   Signature: `decodeBuildMeta(data []byte) (*BuildMeta, error)`
    *   Decode a byte slice back into a `*BuildMeta`.
    *   Return a non-nil `*BuildMeta` and nil error on successful decoding.
    *   Handle invalid or unrecognized data by returning an appropriate error.

*   Ensure the following behaviors:
    *   A round-trip of encoding then decoding must preserve the values of `CJS`, `HasCSS`, `ExportDefault`, `Dts`, and `Imports` fields exactly.
    *   Encoding and then decoding an empty `BuildMeta` (zero-value struct) should result in:
        *   All boolean fields (`CJS`, `HasCSS`, `TypesOnly`, `ExportDefault`) set to false.
        *   `Dts` as an empty string.
        *   `Imports` with zero length.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.