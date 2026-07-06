Implement the hstr crate as an internal component of your repository to manage immutable string types optimized for fast hashing and comparison. Ensure the crate is structured to allow efficient interning of strings and supports the necessary functionality for testing and maintenance.

*   Create the hstr crate at `crates/hstr` and integrate it into the workspace.
*   Export the following public types from `crates/hstr/src/lib.rs`:
    *   `Atom`: An immutable string type optimized for hashing and comparison.
    *   `AtomStore`: A store that manages `Atom` instances.
*   Implement `AtomStore` with the following characteristics:
    *   Must implement the `Default` trait, providing a `default()` method to create a new, independent store instance.
    *   Provide an `atom` method: `fn atom<'a>(&mut self, text: impl Into<Cow<'a, str>>) -> Atom`.
    *   Ensure two calls to `atom()` on the same `AtomStore` with the same string content produce `Atoms` with equal `unsafe_data` fields.
    *   Ensure two calls to `atom()` on different `AtomStore` instances with the same string content produce `Atoms` with different `unsafe_data` field values.
*   Implement `Atom` with the following characteristics:
    *   Must implement `Clone`, `PartialEq`, `Eq`, and `Debug`.
    *   Include a private field named `unsafe_data` that supports `PartialEq` comparison.
    *   Implement a private method `get_hash(&self) -> u64` that returns a consistent hash value for `Atoms` representing the same string, regardless of the originating `AtomStore`.
    *   Ensure `Atoms` remain valid and usable (cloneable, comparable) after their originating `AtomStore` is dropped.
*   Ensure `Atoms` from different `AtomStore` instances that represent the same string compare as equal via `PartialEq`.
*   Create a test module at `crates/hstr/src/tests.rs`:
    *   Include it from `lib.rs` under `#[cfg(test)]` as `mod tests`.
    *   Use `use crate::{Atom, AtomStore};` and access the private `unsafe_data` field and `get_hash()` method directly within tests.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.