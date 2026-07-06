Implement support for skipping fields in WIT derive macros by recognizing a `#[witty(skip)]` attribute. This attribute will allow developers to exclude specific fields from WIT serialization, ensuring that only non-skipped fields participate in type layout, loading, and storing operations.

*   Update the derive macros for WIT type serialization:
    *   Register the `#[witty(skip)]` attribute in the `attributes(...)` list for `proc_macro_derive(WitType, ...)`, `proc_macro_derive(WitLoad, ...)`, and `proc_macro_derive(WitStore, ...)` in `linera-witty-macros/src/lib.rs`.

*   Modify `wit_type.rs`:
    *   In `derive_for_struct<'input>(fields: impl Into<FieldsInformation<'input>>) -> TokenStream`, ensure SIZE and Layout only include non-skipped fields.
    *   In `derive_for_enum<'variants>(name: &Ident, variants: impl DoubleEndedIterator<Item = &'variants Variant> + Clone) -> TokenStream`, ensure each variant's HList only includes non-skipped field types.

*   Modify `wit_load.rs`:
    *   In `derive_for_struct<'input>(fields: impl Into<FieldsInformation<'input>>) -> TokenStream`, generate `load` and `lift_from` methods that:
        *   Bind only non-skipped fields from the HList.
        *   Initialize skipped fields with `Default::default()` after loading.
        *   Construct the full struct using all field names, including defaults.
    *   In `derive_for_enum<'variants>(name: &Ident, variants: impl DoubleEndedIterator<Item = &'variants Variant> + Clone) -> TokenStream`, generate match arms that load non-skipped fields and initialize skipped fields with `Default::default()`.

*   Modify `wit_store.rs`:
    *   In `derive_for_struct<'input>(fields: impl Into<FieldsInformation<'input>>) -> TokenStream`, generate `store` and `lower` methods with destructuring patterns that:
        *   Use `{ field1, field2, .. }` for named structs with skipped fields.
        *   Use `_` wildcards for skipped positions in tuple structs.
    *   In `derive_for_enum<'variants>(name: &Ident, variants: impl DoubleEndedIterator<Item = &'variants Variant> + Clone) -> TokenStream`, generate match arm patterns that skip ignored fields using `..` for named variants and `_` for tuple variants.

*   Introduce `FieldsInformation` struct in `linera-witty-macros/src/util/fields.rs`:
    *   Implement `From<&Fields>` and provide methods:
        *   `non_skipped_fields(&self) -> impl Iterator<Item = &FieldInformation<'_>> + Clone + '_`
        *   `hlist_type(&self) -> TokenStream`
        *   `hlist_bindings(&self) -> TokenStream`
        *   `hlist_value(&self) -> TokenStream`
        *   `construction(&self) -> TokenStream`
        *   `destructuring(&self) -> TokenStream`
        *   `bindings_for_skipped_fields(&self) -> TokenStream`
    *   Publicly re-export from `linera-witty-macros/src/util/mod.rs`.

*   Implement `FieldInformation` struct in `linera-witty-macros/src/util/fields.rs`:
    *   Provide methods:
        *   `name(&self) -> &Ident`
        *   `is_skipped(&self) -> bool`
        *   Deref to `&Field`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.