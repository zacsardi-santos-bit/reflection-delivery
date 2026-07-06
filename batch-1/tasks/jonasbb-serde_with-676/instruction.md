Implement JSON schema generation support for several wrapper types in a Rust serialization helper library. Ensure that each wrapper type correctly generates a JSON schema that aligns with its intended serialization behavior.

*   Implement `JsonSchema` trait for the following wrapper types in `serde_with/src/schemars_0_8.rs`:
    *   `WrapSchema<Cow<'a, T>, BorrowCow>`: Delegate to the schema of the underlying Cow type.
    *   `WrapSchema<T, Bytes>`: Produce the same schema as `Vec<u8>`.
    *   `WrapSchema<T, DefaultOnNull<TA>>`: Produce a nullable schema for the inner type.
    *   `WrapSchema<T, StringWithSeparator<SEP, TA>>`: Produce a plain string schema.
    *   `WrapSchema<O, FromInto<T>>` and `WrapSchema<O, FromIntoRef<T>>`: Produce the schema for the target conversion type.
    *   `WrapSchema<Vec<(K, V)>, Map<KA, VA>>` and related collection types: Produce the same schema as `BTreeMap<K, V>`.
    *   `WrapSchema<[(K, V); N], Map<KA, VA>>`: Produce the same object schema as the Vec variant.
    *   `WrapSchema<T, SetLastValueWins<TA>>`: Produce an array schema without the `uniqueItems` constraint.
    *   `WrapSchema<T, SetPreventDuplicates<TA>>`: Produce an array schema with `uniqueItems: true`.

*   Create new snapshot JSON files in `serde_with/tests/schemars_0_8/snapshots/`:
    *   `bytes.json`: Schema for `Vec<u8>` wrapped with `Bytes`.
    *   `default_on_null.json`: Schema for `String` wrapped with `DefaultOnNull`.
    *   `string_with_separator.json`: Schema for `Vec<String>` wrapped with `StringWithSeparator`.
    *   `from_into.json`: Schema for `u32` wrapped with `FromInto<u64>`.
    *   `map.json` and `map_fixed.json`: Schema for sequences of key-value pairs wrapped with `Map`.
    *   `set_last_value_wins.json`: Schema for `BTreeSet<u32>` wrapped with `SetLastValueWins`.
    *   `set_prevent_duplicates.json`: Schema for `BTreeSet<u32>` wrapped with `SetPreventDuplicates`.

*   Ensure all snapshot JSON files conform to JSON Schema draft-07 and end with a trailing newline.
*   Update existing snapshot files to include a trailing newline:
    *   `serde_with/tests/schemars_0_8/schemars_basic.json`
    *   `serde_with/tests/schemars_0_8/test_std/map.json`
    *   `serde_with/tests/schemars_0_8/test_std/option.json`
    *   `serde_with/tests/schemars_0_8/test_std/set.json`
    *   `serde_with/tests/schemars_0_8/test_std/tuples.json`
    *   `serde_with/tests/schemars_0_8/test_std/vec.json`
    *   `serde_with/tests/schemars_0_8/test_std/vec_deque.json`

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.