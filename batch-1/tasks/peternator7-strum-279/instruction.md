Implement a derive macro `EnumTable` that generates a companion table type for unit-variant enums, allowing type-safe, enum-indexed data storage. Ensure the generated table supports various construction methods, indexed access, cloning, transformations, and operations on optional and result-typed slots.

*   Create the `EnumTable` derive macro, accessible as `strum::EnumTable`, which generates a struct `{EnumName}Table<T>` for any unit-variant enum `EnumName`.
    *   The generated struct must derive `Debug`, `Clone`, `Default`, `PartialEq`, `Eq`, and `Hash`.
    *   Ensure the macro is registered as `#[proc_macro_derive(EnumTable, attributes(strum))]` in `strum_macros/src/lib.rs` and implemented in `strum_macros/src/macros/enum_table.rs`.
    *   Re-export the macro from the main `strum` crate.

*   Implement the following methods for `{EnumName}Table<T>`:
    *   `new(field_per_non_disabled_variant: T, ...) -> {EnumName}Table<T>`: Accepts one parameter per non-disabled variant, using snake_case prefixed with an underscore for variant names.
    *   `filled(value: T) -> {EnumName}Table<T>`: Sets all slots to a clone of `value` (requires `T: Clone`).
    *   `from_closure<F: Fn(EnumName) -> T>(func: F) -> {EnumName}Table<T>`: Calls `func` with each non-disabled variant and stores the result.
    *   `transform<U, F: Fn(EnumName, &T) -> U>(&self, func: F) -> {EnumName}Table<U>`: Returns a new table by applying `func` to each variant and its current value.

*   Implement methods for tables with optional and result-typed slots:
    *   `all(self) -> Option<{EnumName}Table<T>>` for `{EnumName}Table<Option<T>>`: Returns `Some` if all slots are `Some`, otherwise `None`.
    *   `all_ok(self) -> Result<{EnumName}Table<T>, E>` for `{EnumName}Table<Result<T, E>>`: Returns `Ok` with all inner values if all slots are `Ok`, or the first `Err` encountered.

*   Implement trait support:
    *   `Index<EnumName>` for `{EnumName}Table<T>`: `map[variant]` returns `&T`. Panics if `variant` is disabled.
    *   `IndexMut<EnumName>` for `{EnumName}Table<T>`: `map[variant] = value` sets the slot. Panics if `variant` is disabled.

*   Handle disabled variants:
    *   Variants marked `#[strum(disabled)]` are excluded from the table.
    *   Indexing with a disabled variant must panic with the message: `Can't use \`{VariantName}\` with \`{TableName}\` - variant is disabled for Strum features`.

*   Ensure compatibility with Rust reserved keywords:
    *   Use snake_case prefixed with an underscore for field names in the generated struct (e.g., `Const` → `_const`).

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.