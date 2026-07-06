Implement a helper function to correctly parse dynamic trait object types in older Rust editions. Update the parser logic to handle various syntactic forms and ensure the test coverage reflects these changes.

*   Add the `is_dyn_weak` function in `crates/parser/src/grammar/types.rs`:
    *   Signature: `is_dyn_weak(p: &Parser<'_>) -> bool`
    *   Ensure it is visible within the module and can be compiled.
    *   Function should determine if the current parser position is at a contextual `dyn` keyword and if the lookahead indicates the start of a dynamic trait type.

*   Update the parser test `dyn_trait_type_weak`:
    *   Located via the inline comment `// test dyn_trait_type_weak 2015` in `crates/parser/src/grammar/types.rs`.
    *   Ensure the test passes by updating the test data files:
        *   Input file: `crates/parser/test_data/parser/inline/ok/dyn_trait_type_weak.rs`
        *   Expected output file: `crates/parser/test_data/parser/inline/ok/dyn_trait_type_weak.rast`
    *   Include the following forms in the test input:
        *   Plain `dyn Path` form
        *   Reference `&dyn Path` form
        *   Lifetime-bound form `dyn 'a + Path`
        *   Question-mark form `dyn ?Path`
        *   For-binder form `dyn for<'a> Path`
        *   Parenthesized form `dyn(Path)`
        *   Path-qualified form `dyn::Path`
        *   Generic-argument form `dyn<Path>`

*   Parsing requirements in Rust 2015 edition mode:
    *   Forms `dyn Path`, `&dyn Path`, `dyn 'a + Path`, `dyn ?Path`, `dyn for<'a> Path`, and `dyn(Path)` should produce a `DYN_TRAIT_TYPE` node in the parse tree.
    *   Forms `dyn::Path` and `dyn<Path>` should be parsed as ordinary `PATH_TYPE` nodes, treating `dyn` as an identifier segment.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.