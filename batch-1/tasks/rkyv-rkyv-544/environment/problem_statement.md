## Description

When working with rkyv, there is currently no ergonomic way to archive types that come from external crates or other modules that you don't own and cannot modify. To archive such "remote" types, a developer currently has to write all the archiving, serializing, and deserializing trait implementations by hand — which is verbose, error-prone, and hard to maintain.

## Expected Behavior

- Developers should be able to define a local "mirror" struct or enum that reflects the shape of a remote type and, with a single annotation, have the derive macros automatically generate all the required serialization and deserialization machinery for that remote type.
- Both named structs, tuple structs, unit structs, and enums should be supported as mirror types.
- It should be possible to define a "partial" mirror that only covers a subset of the remote type's fields, with the remaining fields filled in by a conversion the developer supplies.
  - For named structs, any subset of fields may be omitted.
  - For tuple structs, only trailing fields may be omitted.
  - For enums, all variants must be present, but fields within each variant may be omitted.
- When a remote type has private fields that cannot be accessed directly, developers should be able to specify getter functions (either returning by value or by reference) to read those fields during archiving.
- Custom field-level wrappers should continue to work when used together with the remote type annotation.

## Why This Matters

Without this feature, archiving third-party types requires writing large amounts of boilerplate for every remote type, which is tedious and easy to get wrong. This feature brings the same ergonomic derive-based workflow to remote types that already exists for types you own, making rkyv practical for a much wider range of real-world use cases.
