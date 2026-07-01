Implement formal verification support for standard Rust hash maps in the Verus library. Ensure that developers can prove properties about hash map operations and the standard hash function. Provide specifications for primitive key types and enable custom key types with explicit assumptions.

*   Create the module `vstd::std_specs::hash`:
    *   Export a broadcast group named `group_hash_axioms` for hash map operations.
    *   Define the spec function `obeys_key_model::<K>()` as a precondition for custom key types.
    *   Implement `DefaultHasher` with a `View` type `Seq<Seq<u8>>`.
    *   Ensure `finish()` returns consistent results for identical views.

*   Implement verification for `HashMap<u32, i8>`:
    *   Verify operations like `new()`, `insert`, `contains_key`, `len()`, `get()`, `remove()`, and `clear()`.

*   Support `HashMap` with `Box<Q>` keys:
    *   Allow operations like `contains_key(&Q)`, `get(&Q)`, and `remove(&Q)` with unboxed references.

*   Enable verification for custom key types:
    *   Require `obeys_key_model::<MyStruct>()` for `HashMap<MyStruct, Value>`.
    *   Ensure verification fails without this assumption.

*   Define `HashMapWithView` in `vstd::hash_map`:
    *   Require `obeys_key_model::<Key>()` and equal views for equal keys.
    *   Implement methods like `insert`, `remove`, `contains_key`, `get`, `clear`, and `len()`.

*   Provide `StringHashMap` in `vstd::hash_map`:
    *   Use `Map<Seq<char>, Value>` as the view type.
    *   Implement methods for `String` keys and `&str` queries.

*   Define broadcast proofs:
    *   `axiom_hash_map_with_view_spec_len` for `HashMapWithView`.
    *   `axiom_string_hash_map_spec_len` for `StringHashMap`.

*   Update `source/vstd/std_specs/mod.rs` and `source/vstd/vstd.rs` to register new modules.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.