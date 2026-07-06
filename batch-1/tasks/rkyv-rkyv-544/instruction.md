Implement a feature that allows archiving of remote types using rkyv by defining local mirror types with derive macros. Ensure that these mirror types can automatically generate the necessary serialization and deserialization machinery for remote types, supporting various struct and enum types, including partial mirrors and private field access through getters.

*   Annotate structs or enums with `#[derive(Archive, Serialize, Deserialize)]` and `#[rkyv(remote = TypePath)]` to generate `ArchiveWith<TypePath>`, `SerializeWith<TypePath, S>`, and `DeserializeWith<ArchivedType, TypePath, D>` implementations.
    *   `TypePath` is the path to the remote type.
*   Support named structs with any subset of fields from the remote type.
    *   Omitted fields must be provided in the `From<WrapperType>` implementation.
*   Support tuple structs with partial fields, allowing omission of only trailing fields.
*   Ensure unit structs derive correctly for their corresponding remote unit type.
*   Require enums to include all variants from the remote enum.
    *   Allow omission of fields within each variant.
*   Implement field-level `#[rkyv(getter = path::to::fn)]` attribute for accessing private fields.
    *   Support getters returning values by value or by reference.
*   Ensure `#[rkyv(with = WrapType)]` continues to work on fields within remote wrapper types.
*   After deserialization, convert the archived form to the remote type using `From<WrapperType>`.
*   Ensure the archive-then-deserialize roundtrip reproduces the original remote value exactly.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.