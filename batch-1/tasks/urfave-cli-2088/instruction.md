Implement a complete and consistent positional argument system for a CLI library. Extend support to all standard Go integer sizes, both signed and unsigned, and ensure each type has both single-value and multi-value variants. Additionally, provide type-safe retrieval methods for positional arguments by name.

*   Implement single-value positional argument types:
    *   `IntArg`, `UintArg`, `StringArg`, `TimestampArg` with fields `Name`, `UsageText`, `Destination`.
    *   Parsing for `IntArg` and `UintArg` must reject float-format strings.
    *   `Usage()` method should return `UsageText` if set, otherwise `Name`.

*   Implement multi-value positional argument types:
    *   `IntArgs`, `UintArgs`, `FloatArgs`, `StringArgs`, `TimestampArgs` with fields `Name`, `Min`, `Max`, `Destination`.
    *   Parsing for `IntArgs` and `UintArgs` must reject float-format strings with an error message containing `strconv.ParseInt: parsing "10.0": invalid syntax`.
    *   Ensure `StringArgs` replaces `Values *[]string` with `Destination *[]string`, setting `*Destination` to `[]string{}` when `Min=0` and no arguments are provided.

*   Implement slice variants for all integer sizes:
    *   `Int8Args`, `Int16Args`, `Int32Args`, `Int64Args`, `Uint8Args`, `Uint16Args`, `Uint32Args`, `Uint64Args`.

*   Update `Command` struct to include accessor methods:
    *   Singular accessors: `IntArg`, `Int8Arg`, `Int16Arg`, `Int32Arg`, `Int64Arg`, `UintArg`, `Uint8Arg`, `Uint16Arg`, `Uint32Arg`, `Uint64Arg`, `FloatArg`, `StringArg`, `TimestampArg`.
        *   Return Go zero value if the argument name is not found or type does not match.
    *   Slice accessors: `IntArgs`, `Int8Args`, `Int16Args`, `Int32Args`, `Int64Args`, `UintArgs`, `Uint8Args`, `Uint16Args`, `Uint32Args`, `Uint64Args`, `FloatArgs`, `StringArgs`, `TimestampArgs`.
        *   Return `nil` if the argument name is not found or type does not match.
        *   Return a non-nil empty slice if the argument was parsed but received no values.

*   Ensure `Command` struct looks up arguments by name using `HasName(string) bool` and retrieves values with `Get() any`.
    *   `Argument` interface must include `HasName(string) bool`, `Parse([]string) ([]string, error)`, `Usage() string`, `Get() any`.

*   Provide a new generic base type `ArgumentsBase[T, C, VC]` for slice arguments, separate from `ArgumentBase[T, C, VC]` for single-value arguments.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.