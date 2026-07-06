Extend the Cairo documentation extraction system to include both outer and inner comments for functions and modules, and capture file-level comments for crates and non-inline submodules. Ensure that the language server's hover tooltips display the complete combined documentation.

*   Update the `DocumentableItemId` enum:
    *   Add a `Crate(CrateId)` variant.
    *   Implement `stable_location` to return `None` for the `Crate` variant and `Some(location)` for others.
    *   Implement `From<CrateId>` to convert `CrateId` into `DocumentableItemId::Crate`.

*   Modify the `DocGroup` trait:
    *   Add `FilesGroup` and `DefsGroup` as supertraits.
    *   Ensure `get_item_documentation` and `get_item_signature` methods are available.

*   Implement `get_item_documentation` method:
    *   For `DocumentableItemId::Crate(crate_id)`:
        *   Return file-level inner comments from the crate root file, stripped of comment markers and joined by a space.
        *   Return `None` if no comments exist.
    *   For free, trait, and impl functions:
        *   Combine outer '///' comments and inner '//!' comments, with outer comments first, separated by a space.
        *   Return `None` if neither exists.
    *   For inline modules:
        *   Combine outer '///' comments and inner '//!' comments from the module body, joined by a space.
        *   Return `None` if neither exists.
    *   For non-inline submodules:
        *   Combine file-level '//!' comments from the module's source file and outer '///' comments from the module declaration, joined by a space.
        *   Return `None` if neither exists.
    *   For traits, impl blocks, structs, and enums:
        *   Return only outer '///' comments, joined by a space.
        *   Return `None` if no comments exist.
    *   For struct members and enum variants:
        *   Return only the outer '///' comment for that member/variant.
        *   Return `None` if none exists.

*   Ensure the language server's hover tooltip:
    *   Displays both outer and inner comments for functions, with outer comments first and inner comments second, separated by a space.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.