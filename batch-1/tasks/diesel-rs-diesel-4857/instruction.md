Implement support for optional embedded structs in Diesel's update changeset derive functionality. Ensure that when an embedded struct is wrapped in an optional type, it correctly updates the database when present and leaves columns unchanged when absent.

*   Implement the `AsChangeset` trait for references to `Option<T>` in `diesel/src/query_builder/update_statement/changeset.rs`.
    *   Use the signature: `impl<'update, T> AsChangeset for &'update Option<T> where &'update T: AsChangeset`.
    *   Ensure the associated type `Target` is `<&'update T as AsChangeset>::Target`.
    *   Ensure the associated type `Changeset` is `Option<<&'update T as AsChangeset>::Changeset>`.
    *   Implement the `as_changeset` method to convert `&Option<T>` into `Option<Changeset>` by mapping over the inner value.

*   Ensure that:
    *   When a struct field is annotated with the embed attribute and wrapped in an optional type, the outer struct's `AsChangeset` derivation compiles and functions correctly.
    *   When the optional embedded field is present, all columns from the embedded struct are updated in the database along with the outer struct's columns.
    *   When the optional embedded field is absent, the corresponding database columns remain unchanged after the update.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.