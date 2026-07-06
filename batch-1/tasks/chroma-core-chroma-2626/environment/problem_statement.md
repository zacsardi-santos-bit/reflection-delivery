## Description

In Chroma's data model, every segment must belong to a collection — there is no valid scenario in which a segment exists without an associated collection. However, the current implementation treats the segment's collection reference as optional (nullable) throughout the codebase. This means the serialized network representation of a segment can have a null or missing collection field, and various parts of the code need to handle the null case explicitly.

This inconsistency should be resolved by making the collection field on segments required everywhere:

- The network message format for a segment should declare the collection field as a required, non-optional string (not optional/pointer).
- When a segment is converted to its wire format, the collection identifier must always be populated — using the nil UUID string (all zeros) as a fallback when no specific collection has been set.
- Segment creation and update logic should no longer accept or produce null collection values.
- Collection objects used during segment construction should be accessed using proper attribute/property access rather than generic key/dictionary lookup.

## Expected Behavior

- Serializing a segment with a nil/zero collection ID should produce a network message where the collection field equals the nil UUID string (all zeros), not a null or absent field.
- The network Segment message's collection field type should be a non-optional plain string.
- No code path should allow creating or updating a segment with a null collection reference.

## Why This Matters

Treating the collection field as optional when it is effectively required creates unnecessary complexity and potential bugs. Enforcing it as required in the data model, the network schema, and the conversion logic makes the system more consistent and removes special-case null handling that shouldn't be needed.
