## Description

The derive macros for converting Rust types to and from Nushell values currently only support renaming fields/variants at the container level (applying a case conversion to all fields at once). There is no way to rename a single individual field or enum variant to a specific custom string key in the resulting Nushell value. This is a gap compared to similar serialization crates that support per-member renaming.

Additionally, there are two related problems:

1. **Incorrect error categorization**: When an unrecognized attribute argument is placed on a named struct field or an enum variant, the macro currently reports the wrong kind of error. The correct error for "unrecognized attribute key" is distinct from the error for "attribute placed in an unsupported position", but the two were previously conflated.

2. **No duplicate-name detection**: If two fields or variants end up resolving to the same key name — whether through explicit renaming, case conversion, or a combination — the macro silently generates broken code instead of reporting an error at compile time.

## Expected Behavior

- A per-member rename attribute should be supported, allowing a developer to specify a custom string key for an individual struct field or enum variant independently of the container-level settings.
- When an unrecognized attribute argument appears on a named field or enum variant, the error reported should be the "unexpected attribute" kind.
- When an attribute appears on a field of a tuple struct (positional/unnamed fields), the error reported should remain the "invalid attribute position" kind — these two error categories must be correctly distinguished.
- When two fields or variants would resolve to the same name, the derive macro must detect the conflict and report a "non-unique name" error at compile time, rather than producing incorrect code.

## Why This Matters

Without per-member renaming, developers are forced to restructure their types or add boilerplate conversion code whenever a field's Rust name does not match the desired Nushell record key. The duplicate-name detection prevents subtle runtime bugs where a rename silently creates a name collision.
