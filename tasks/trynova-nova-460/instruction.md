Implement the missing clamped byte type and associate each binary type with its corresponding JavaScript prototype in the typed array system. Ensure that the engine can properly handle typed array prototype lookups and support the clamped byte array type to resolve conformance test crashes.

*   Update the `Viewable` trait in `nova_vm/src/ecmascript/types/spec/data_block.rs`:
    *   Add a required associated constant `PROTO` of type `ProtoIntrinsics`.
    *   Set a default value of `false` for the existing `IS_BIGINT` constant.

*   Define a new `U8Clamped` struct:
    *   Location: `nova_vm/src/ecmascript/types/spec/data_block.rs`.
    *   Structure: `pub(crate) struct U8Clamped(pub u8)`.
    *   Derive `Debug`, `Clone`, and `Copy`.

*   Implement the `Sealed` trait for `U8Clamped`.

*   Implement the `Viewable` trait for `U8Clamped`:
    *   Set `PROTO = ProtoIntrinsics::Uint8ClampedArray`.
    *   Use `to_uint8_clamp` for `from_be_value` and `from_le_value`, wrapping the result in `U8Clamped`.
    *   Delegate `into_be_value` and `into_le_value` to the inner `u8` field's byte-order methods.

*   Update existing `Viewable` implementations to provide the `PROTO` constant:
    *   `u8` → `ProtoIntrinsics::Uint8Array`
    *   `i8` → `ProtoIntrinsics::Int8Array`
    *   `u16` → `ProtoIntrinsics::Uint16Array`
    *   `i16` → `ProtoIntrinsics::Int16Array`
    *   `u32` → `ProtoIntrinsics::Uint32Array`
    *   `i32` → `ProtoIntrinsics::Int32Array`
    *   `u64` → `ProtoIntrinsics::BigUint64Array`
    *   `i64` → `ProtoIntrinsics::BigInt64Array`
    *   `f32` → `ProtoIntrinsics::Float32Array`
    *   `f64` → `ProtoIntrinsics::Float64Array`

*   Import necessary functions and types:
    *   `to_uint8_clamp` from `ecmascript::abstract_operations::type_conversion` in `data_block.rs`.
    *   `ProtoIntrinsics` from `ecmascript::execution` in `data_block.rs`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.