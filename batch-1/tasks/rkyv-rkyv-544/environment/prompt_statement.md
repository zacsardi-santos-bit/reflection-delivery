I'm trying to use rkyv to archive types from external crates that I don't control. Right now there's no clean way to do this with the derive macros — I'd have to manually implement all the archiving, serializing, and deserializing traits, which is a lot of boilerplate. I'd love a way to define a local mirror type that reflects the structure of the remote type and have the derive macros generate all the necessary trait implementations automatically.

The feature should work for named structs, tuple structs, unit structs, and enums. It should also allow "partial" mirrors where I only include the fields I care about, and fill in the rest myself through a conversion. For named structs, I should be able to omit any fields; for tuple structs, only trailing fields should be omittable; for enums, all variants must be present but variant fields can be omitted.

A related pain point is private fields on remote types — I have types where fields are not publicly accessible. In those cases, I need a way to point the derive machinery at a getter function that can read the private field on my behalf, and it should work whether the getter returns the value directly or returns a reference.

Existing per-field wrappers should still work alongside this new annotation. The full cycle of archiving and then deserializing back to the original remote type should preserve the data correctly.
