Redesign the value-source system for CLI flags to improve consistency and traceability. Implement a new interface and struct types to provide detailed source information and error messages. Update helper functions and internal struct visibility for a cleaner API.

*   Implement the `ValueSource` interface in `value_source.go` with the following methods:
    *   `Lookup() (string, bool)` to retrieve a value and indicate if it was found.
    *   `String() string` for human-readable output, implementing `fmt.Stringer`.
    *   `GoString() string` for Go-syntax output, implementing `fmt.GoStringer`.

*   Create the `ValueSourceChain` struct in `value_source.go`:
    *   Include a field `Chain` of type `[]ValueSource`.
    *   Implement `ValueSource`, `fmt.Stringer`, and `fmt.GoStringer`.
    *   Implement `Lookup() (string, bool)` to return the first resolved value.
    *   Implement `LookupWithSource() (string, ValueSource, bool)` to return the value, the source, and a found indicator.
    *   `String()` should return a comma-separated list of each source's `String()` value, or an empty string if `Chain` is empty.
    *   `GoString()` should return a formatted string like `&ValueSourceChain{Chain:{...}}` with each source's `GoString()` value.

*   Update helper functions:
    *   Implement `EnvVars(keys ...string) ValueSourceChain` in `value_source.go`:
        *   Return a `ValueSourceChain` with `Chain` containing `envVarValueSource` for each key.
    *   Implement `Files(paths ...string) ValueSourceChain` in `value_source.go`:
        *   Return a `ValueSourceChain` with `Chain` containing `fileValueSource` for each path.

*   Define the `envVarValueSource` struct in `value_source.go`:
    *   Unexported with a field `Key string`.
    *   Implement `Lookup() (string, bool)` to check the environment variable.
    *   `String()` returns `environment variable "KEY"`.
    *   `GoString()` returns `&envVarValueSource{Key:"KEY"}`.

*   Define the `fileValueSource` struct in `value_source.go`:
    *   Unexported with a field `Path string`.
    *   Implement `Lookup() (string, bool)` to read the file at `Path`.
    *   `String()` returns `file "PATH"`.
    *   `GoString()` returns `&fileValueSource{Path:"PATH"}`.

*   Modify the `Sources` field on flag types (`FlagBase`) to use `ValueSourceChain` instead of the old slice type.
*   Ensure `EnvVars()` can be directly assigned to the `Sources` field.
*   Generate error messages for parse failures with the pattern: `could not parse "VALUE" as TYPE value from environment variable "NAME" for flag FLAGNAME:`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.