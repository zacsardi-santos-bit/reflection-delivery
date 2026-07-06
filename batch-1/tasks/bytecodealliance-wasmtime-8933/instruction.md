Implement a host-side API for WebAssembly GC struct types in Wasmtime to enable Rust host code to define, allocate, manipulate, and exchange struct values with WebAssembly modules. Ensure that the API integrates with the existing garbage-collection rooting discipline and supports interaction with WebAssembly functions, globals, and tables.

*   Define Struct Types:
    *   Implement `StructType::new(engine: &Engine, fields: impl IntoIterator<Item = FieldType>) -> Result<StructType>`.
    *   Support zero or more fields with varying storage types (I8, I32, ANYREF, EXTERNREF).

*   Allocate Struct Instances:
    *   Implement `StructRefPre::new(store: impl AsContextMut, struct_ty: StructType) -> StructRefPre`.
    *   Implement `StructRef::new(store: impl AsContextMut, pre: &StructRefPre, fields: &[Val]) -> Result<Rooted<StructRef>>`.
    *   Ensure all field values are rooted and belong to the same store.
    *   Return an error for unrooted references; panic with 'wrong store' for mismatched stores.

*   Field Access and Modification:
    *   Implement `StructRef::field(&self, store: impl AsContextMut, index: usize) -> Result<Val>`.
        *   Return stored value for valid indices; error for out-of-bounds or unrooted references.
    *   Implement `StructRef::set_field(&self, store: impl AsContextMut, index: usize, val: Val) -> Result<()>`.
        *   Succeed for valid mutable fields; return errors for out-of-bounds, unrooted references, cross-store values, immutable fields, or type mismatches.

*   Retrieve Struct Type and Iterate Fields:
    *   Implement `StructRef::ty(&self, store: impl AsContext) -> Result<StructType>`.
        *   Return error for unrooted references.
    *   Implement `StructRef::fields(&self, store: impl AsContextMut) -> Result<impl ExactSizeIterator<Item = Val>>`.
        *   Return error for unrooted references.

*   Struct Reference Handling:
    *   Implement `AnyRef::is_struct(&self, store: impl AsContext) -> Result<bool>`.
    *   Implement `AnyRef::as_struct(&self, store: impl AsContext) -> Result<Option<Rooted<StructRef>>>`.
    *   Implement `AnyRef::unwrap_struct(&self, store: impl AsContext) -> Result<Rooted<StructRef>>`.
    *   Ensure `Rooted<StructRef>` is convertible to `Rooted<AnyRef>` and `Val`.

*   WebAssembly Integration:
    *   Ensure struct references can be passed through WebAssembly function calls, stored in globals and tables, and instantiated with host-created globals.
    *   Implement `HeapType::ConcreteStruct(StructType)` for constructing RefType values.

*   Additional Requirements:
    *   Implement `Rooted::ref_eq(store: impl AsContext, a: &Rooted<T>, b: &Rooted<T>) -> Result<bool>` for `Rooted<StructRef>`.
    *   Accept `Val::null_any_ref()` and `Val::null_extern_ref()` as valid null field values.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.