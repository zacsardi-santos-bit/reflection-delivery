Implement the changes to the component bundle interface in the Rerun core types crate to improve safety and organization. Move the `AsComponents` trait to a dedicated module, remove the blanket implementation, and ensure explicit handling of single components as bundles.

*   Move the `AsComponents` trait:
    *   Relocate from `crates/store/re_types_core/src/lib.rs` to `crates/store/re_types_core/src/as_components.rs`.
    *   Declare as a private module in `lib.rs` with `mod as_components;`.
    *   Re-export publicly with `pub use self::as_components::AsComponents`.

*   Update the `AsComponents` trait:
    *   Define the method: `fn as_component_batches(&self) -> Vec<ComponentBatchCowWithDescriptor<'_>>`.
    *   Remove the blanket implementation `impl<C: Component> AsComponents for C`.

*   Implement `AsComponents` for:
    *   `dyn ComponentBatch`: Return a single-element `Vec` wrapping `self`.
    *   Const-generic arrays of `&dyn ComponentBatch`: `[&dyn ComponentBatch; N]`.
    *   Standard collection forms of component batches:
        *   `[Box<dyn ComponentBatch>; N]`
        *   `&[&dyn ComponentBatch]`
        *   `&[Box<dyn ComponentBatch>]`
        *   `Vec<&dyn ComponentBatch>`
        *   `Vec<Box<dyn ComponentBatch>>`
    *   Standard collection forms that implement `AsComponents`:
        *   `[AS; N]` where `AS: AsComponents`
        *   `[&dyn AsComponents; N]`
        *   `[Box<dyn AsComponents>; N]`
        *   `&[AS]` where `AS: AsComponents`
        *   `&[&dyn AsComponents]`
        *   `&[Box<dyn AsComponents>]`
        *   `Vec<AS>` where `AS: AsComponents`
        *   `Vec<&dyn AsComponents>`
        *   `Vec<Box<dyn AsComponents>>`

*   Ensure serialization:
    *   A single component as `&dyn ComponentBatch` serializes to a single-element Arrow array.
    *   A slice of N components serializes to an N-element Arrow array.
    *   A `Vec<Component>` serializes to an Arrow array with all values in order.
    *   An array of `&dyn ComponentBatch` as `&dyn AsComponents` produces one entry per element, each serializing independently.

*   Include a test module in `as_components.rs`:
    *   Add `#[cfg(test)] mod tests` with the path `re_types_core::as_components::tests`.
    *   Cover serialization of single components, slices, and vectors as batch trait objects.
    *   Test arrays of `&dyn ComponentBatch` cast as `&dyn AsComponents`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.