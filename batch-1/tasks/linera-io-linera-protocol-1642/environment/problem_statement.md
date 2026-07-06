# Support Skipping Fields in WIT Derive Macros

## Description

When deriving WIT serialization traits for Rust types, there is currently no way to exclude certain fields from the wire format. Every field in a struct or enum variant must participate in the WIT type layout, loading, and storing operations. This is a problem for types that contain fields serving purely internal purposes — for example, marker types, cached data, or other implementation details that should not cross the WebAssembly interface boundary.

## Expected Behavior

- Developers should be able to annotate individual struct fields or enum variant fields with a skip marker, indicating that the field should be excluded from WIT serialization.
- The type layout (size and memory representation) derived for types with skipped fields should only account for the non-skipped fields.
- When loading a value from WebAssembly memory, skipped fields should be automatically initialized using their default value rather than being read from memory.
- When storing a value to WebAssembly memory, skipped fields should be silently ignored — only the non-skipped fields are written.
- This behavior must work correctly for named structs, tuple structs, and all enum variant kinds (empty, tuple, and named struct variants).

## Why This Matters

Without this feature, types that carry local implementation details (such as marker fields or phantom type parameters) cannot use the automatic WIT derive macros at all — developers must either remove those fields entirely or write manual serialization implementations. Supporting field-level exclusion makes the derive macros applicable to a much wider range of real-world types.
