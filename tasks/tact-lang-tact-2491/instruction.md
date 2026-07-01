Implement a compile-time type check for the `dump()` function in the Tact compiler to ensure it only accepts supported types. Produce a clear error message when an unsupported type is passed to `dump()`.

*   Update the `resolve` function within the "dump" entry of `GlobalFunctions` in `src/abi/global.ts` to:
    *   Validate the type of the argument passed to `dump()` at compile time.
    *   Accept arguments of the following types without error:
        *   Standard primitive reference types: Int, Bool, String, Address, Builder, Cell, Slice.
        *   Null literals, void return values, optional types (e.g., `Int?`), and map types (e.g., `map<Int, Int>`).
    *   Reject arguments of non-standard primitive types (e.g., StringBuilder) and produce a compilation error.
    *   Produce specific error messages:
        *   For unsupported named reference types: 
            *   `Cannot dump() argument with "StringBuilder" type, see https://docs.tact-lang.org/ref/core-debug/#dump for more information`
        *   For unsupported type kinds (non-ref/void/null/map):
            *   `Cannot dump() this argument, see https://docs.tact-lang.org/ref/core-debug/#dump for more information`
        *   For incorrect argument count:
            *   `dump() expects 1 argument, see https://docs.tact-lang.org/ref/core-debug/#dump for more information`

*   Ensure that the `resolve` function checks if the argument's type kind and name are in the set of supported types, throwing a compilation error for unsupported types.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.