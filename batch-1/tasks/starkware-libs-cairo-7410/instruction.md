Implement a method to retrieve formatted item signatures along with type reference positions for the Cairo documentation system. Address existing formatting inconsistencies in signatures, ensuring clean and accurate output.

*   Update the `DocGroup` trait in `crates/cairo-lang-doc/src/db.rs`:
    *   Implement the method `get_item_signature_with_links(&self, id: DocumentableItemId) -> (Option<String>, Vec<LocationLink>)`.
    *   Return a tuple where `Option<String>` is the formatted signature or `None` for items without signatures, and `Vec<LocationLink>` contains position ranges for linked type names.

*   Define the `LocationLink` struct in `crates/cairo-lang-doc/src/documentable_formatter.rs`:
    *   Include public fields `start: usize` and `end: usize` to represent the character range of a linked type reference in the signature.

*   Ensure `get_item_signature_with_links`:
    *   Returns location links for each member type reference in struct items.
    *   Provides links for all referenced types in function parameter and return types.
    *   Includes links for generic parameters and bounds in generic struct items.

*   Handle unresolvable type references:
    *   Display `<missing>` in the signature for unresolved types.
    *   Do not emit `LocationLink` for unresolved types.

*   Correct signature formatting:
    *   End impl block item signatures with a semicolon.
    *   End extern type item signatures with a semicolon.
    *   Omit the return type annotation for functions returning the unit type.
    *   Format function parameter lists on a single line.
    *   Display named generic parameters without the 'impl' keyword or trait bound.

*   Use the function `setup_test_module_without_syntax_diagnostics` in `crates/cairo-lang-doc/src/tests/test_utils.rs` to set up test environments:
    *   Signature: `setup_test_module_without_syntax_diagnostics<T: DefsGroup + AsFilesGroupMut + ?Sized>(db: &mut T, content: &str) -> CrateId`.
    *   Allows test code with undefined types to compile for documentation processing.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.