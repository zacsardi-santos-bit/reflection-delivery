Implement methods to check if filter types are "empty" in the filter package. Ensure each filter type has an `IsEmpty()` method that determines if any conditions are set.

*   Implement `IsEmpty()` for `FilterString`:
    *   Return `true` if all fields (`Eq`, `Ne`, `In`, `Nin`, `Like`, `Nlike`, `Ilike`, `Nilike`, `Gt`, `Gte`, `Lt`, `Lte`, `And`, `Or`) are nil.
    *   Return `false` if any field is non-nil.
    *   Use the signature: `(f FilterString) IsEmpty() bool`.

*   Implement `IsEmpty()` for `FilterInteger`:
    *   Return `true` if all fields (`Eq`, `Ne`, `Gt`, `Gte`, `Lt`, `Lte`, `And`, `Or`) are nil.
    *   Return `false` if any field is non-nil.
    *   Use the signature: `(f FilterInteger) IsEmpty() bool`.

*   Implement `IsEmpty()` for `FilterFloat`:
    *   Return `true` if all fields (`Eq`, `Ne`, `Gt`, `Gte`, `Lt`, `Lte`, `And`, `Or`) are nil.
    *   Return `false` if any field is non-nil.
    *   Use the signature: `(f FilterFloat) IsEmpty() bool`.

*   Implement `IsEmpty()` for `FilterBoolean`:
    *   Return `true` if the `Eq` field is nil.
    *   Return `false` if the `Eq` field is non-nil, regardless of the pointed-to value.
    *   Use the signature: `(f FilterBoolean) IsEmpty() bool`.

*   Implement `IsEmpty()` for `FilterTime`:
    *   Return `true` if all fields (`Gt`, `Gte`, `Lt`, `Lte`, `And`, `Or`) are nil.
    *   Return `false` if any field is non-nil.
    *   Use the signature: `(f FilterTime) IsEmpty() bool`.

*   Ensure all `IsEmpty()` methods are defined on value receivers to allow invocation on both pointer and non-pointer instances.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.