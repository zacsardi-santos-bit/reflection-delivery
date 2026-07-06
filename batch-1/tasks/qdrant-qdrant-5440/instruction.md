Implement a read-only typed slice abstraction for memory-mapped files in the Qdrant codebase. Create a new module to handle read-only memory maps, allowing them to be treated as slices of any fixed-size element type. Ensure that the conversion only succeeds when the file size is a valid multiple of the element size.

*   Create a new public module named `mmap_type_readonly` in `lib/common/memory/src/lib.rs`.
    *   Declare it with `pub mod mmap_type_readonly;`.
    *   Implement the module in a new file `lib/common/memory/src/mmap_type_readonly.rs`.

*   In the `mmap_type_readonly` module:
    *   Define a public struct `MmapSliceReadOnly<T>` where `T: Sized + 'static`.
    *   Implement an `unsafe` associated function `try_from` for `MmapSliceReadOnly<T>`.
        *   Accept a `memmap2::Mmap` as an argument.
        *   Return a `Result` containing `Self` on success.
        *   Ensure `try_from` returns `Ok(Self)` when `mmap.len()` is a multiple of `size_of::<T>()`.
        *   Return `Err` if the size is not a valid multiple of `size_of::<T>()`.
    *   Provide a method `len()` (directly or via `Deref`) that returns the number of `T` elements fitting in the mmap, calculated as `mmap.len() / size_of::<T>()`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.