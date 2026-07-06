Implement support for renaming individual struct fields and enum variants using a per-member attribute in the Nushell derive macros. Ensure proper error categorization for unrecognized attributes and detect naming conflicts at derive time.

*   Implement a per-member rename attribute for struct fields and enum variants:
    *   Use the syntax `#[nu_value(rename = "string_literal")]` to specify a custom string key.
    *   Override the default name used for that field or variant when converting to/from a Nushell value.

*   Update error handling for attribute placement:
    *   Return `DeriveError::UnexpectedAttribute` for unrecognized attribute arguments on named struct fields and enum variants.
    *   Return `DeriveError::InvalidAttributePosition` for attributes on tuple struct fields, ensuring the named field within this variant is `attribute_span`.

*   Implement duplicate-name detection:
    *   Add a new `DeriveError` variant `NonUniqueName` with fields `name` (String), `first` (Span), and `second` (Span).
    *   Detect and return `DeriveError::NonUniqueName` when two struct fields or enum variants resolve to the same string key due to renaming or case conversion.

*   Modify the `DeriveError::InvalidAttributePosition` variant:
    *   Ensure it includes a named field `attribute_span` for pattern matching.

*   Ensure all changes are made in the source files located in `crates/nu-derive-value/src/error.rs` and related macro processing files.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.