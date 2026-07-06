## Description

There are two related issues in the Cadence storage migration system involving legacy type wrappers and dictionary key handling.

**Issue 1: Legacy wrapper equality is broken**

Several legacy wrapper types used during migration to encode values in an old format do not correctly implement equality comparison. When comparing a legacy-wrapped value against another legacy-wrapped value of the same kind, the comparison incorrectly fails or produces wrong results. For example, two legacy-wrapped character values containing the same character are not considered equal, and similarly for legacy-wrapped string values, intersection types, primitive static types, and reference types. Each of these wrappers should recognize when the other side of a comparison is also wrapped in the same legacy wrapper, unwrap it, and perform the comparison against the underlying value.

**Issue 2: Dictionary key conflicts during migration are silently ignored**

When migrating a dictionary whose keys use legacy type encodings, it is possible for two distinct legacy keys to map to the same new key after migration. For example, two intersection types that contain the same interfaces but in different orderings might have been stored as distinct keys in the old encoding, but after migration they hash to the same value. Currently, this causes silent data corruption: one entry silently overwrites the other with no error reported.

## Expected Behavior

- Legacy wrapper types must implement equality such that two wrappers of the same kind wrapping equal underlying values are considered equal.
- During migration, if a dictionary already contains the migrated key after the old key has been removed, the migration must detect this conflict and report an error. The error must be associated with the relevant storage location and key, and must include a stack trace. The migration should report the successfully migrated entries separately from the conflict error.

## Why This Matters

Without these fixes, migration operations can silently corrupt stored data when key collisions occur, and legacy-wrapped type comparisons produce incorrect results, potentially causing further downstream issues during multi-step migrations.
