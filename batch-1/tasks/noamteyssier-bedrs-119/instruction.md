Implement new struct types for the BED format variants in your genomic interval library. Ensure these types are easily constructible, provide field accessors, and integrate with existing library traits. Make them accessible from the crate root for user convenience.

*   Export the following types at the crate root:
    *   `Bed3`, `Bed4`, `Bed6`, `Bed12` (e.g., `bedrs::Bed3`, `bedrs::Bed4`, `bedrs::Bed6`, `bedrs::Bed12`).
    *   `Coordinates` trait as `bedrs::Coordinates`.

*   Implement `Bed3` struct:
    *   Location: `src/types/record/bed3.rs` (re-exported as `bedrs::Bed3`).
    *   Construct using `Bed3::new(chr, start, end)`.
    *   Provide methods:
        *   `.chr(&self) -> &C` for chromosome access.
        *   `.start(&self) -> T` for start coordinate access.
        *   `.end(&self) -> T` for end coordinate access.
    *   Implement the `Coordinates<C, T>` trait.

*   Implement `Bed4` struct:
    *   Location: `src/types/record/bed4.rs` (re-exported as `bedrs::Bed4`).
    *   Construct using `Bed4::new(chr, start, end, name)`.
    *   Provide methods:
        *   `.chr(&self) -> &C`.
        *   `.start(&self) -> T`.
        *   `.end(&self) -> T`.
        *   `.name(&self) -> &N` (or compatible with comparison to `&str` when `N = String`).
    *   Implement the `Coordinates<C, T>` trait.

*   Ensure `Bed6` and `Bed12` structs are importable:
    *   `Bed6` location: `src/types/record/bed6.rs` (re-exported as `bedrs::Bed6`).
    *   `Bed12` location: `src/types/record/bed12.rs` (re-exported as `bedrs::Bed12`).

*   Ensure `Coordinates` trait is importable:
    *   Location: `src/traits/` (re-exported as `bedrs::Coordinates`).
    *   Provide core methods: `.chr()`, `.start()`, and `.end()`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.