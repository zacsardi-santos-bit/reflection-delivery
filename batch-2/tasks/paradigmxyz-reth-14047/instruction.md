Implement a new per-account storage cache type in the existing cached-state module of the engine tree. Ensure it is constructable with a configurable maximum number of storage slots and can report its current size.

*   Define a struct named `AccountStorageCache` in `crates/engine/tree/src/tree/cached_state.rs`.
    *   Ensure it is publicly accessible as `crate::tree::cached_state::AccountStorageCache`.
*   Implement the following methods for `AccountStorageCache`:
    *   `new(max_slots: u64) -> Self`: Accepts a single `u64` argument representing the maximum number of storage slots the cache may hold and returns a new `AccountStorageCache` instance.
    *   `len(&self) -> usize`: Returns a `usize` representing the current number of cached storage slots. For a newly created cache with no insertions, `len()` must return 0.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.