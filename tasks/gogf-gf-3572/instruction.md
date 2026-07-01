Implement the ability to remove elements from thread-safe map and set containers during iteration without causing a deadlock. Ensure that after iteration, the containers accurately reflect the removals made.

*   Update the `Iterator` method for all thread-safe map types in the `container/gmap` package:
    *   `AnyAnyMap` in `gmap_hash_any_any.go`
    *   `IntAnyMap` in `gmap_hash_int_any.go`
    *   `IntIntMap` in `gmap_hash_int_int.go`
    *   `IntStrMap` in `gmap_hash_int_str.go`
    *   `StrAnyMap` in `gmap_hash_str_any.go`
    *   `StrIntMap` in `gmap_hash_str_int.go`
    *   `StrStrMap` in `gmap_hash_str_str.go`
    *   Ensure the method signature is `(m *<MapType>) Iterator(f func(k <KeyType>, v <ValueType>) bool)`.
    *   Ensure the method does not deadlock when `f` calls `Remove` on the same map instance.
    *   Ensure that after iteration, the map accurately reflects the removals.

*   Update the `Iterator` method for all thread-safe set types in the `container/gset` package:
    *   `Set` in `gset.go`
    *   `IntSet` in `gset_int.go`
    *   `StrSet` in `gset_str.go`
    *   Ensure the method signature is `(set *<SetType>) Iterator(f func(v <ValueType>) bool)`.
    *   Ensure the method does not deadlock when `f` calls `Remove` on the same set instance.
    *   Ensure that after iteration, the set accurately reflects the removals.

*   Ensure that these updates apply specifically to instances created with the `safe=true` constructor parameter.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.