## Description

The ORM currently supports plain UUID values as entity model field types — but it does not support the various UUID display format wrapper types as entity model field types. Developers who prefer to work with UUIDs in a specific representation (braced, hyphenated, simple, or URN format) are forced to store plain UUIDs and perform manual conversion after every database read. There is no way to declare an entity model field with a formatted UUID type and have the ORM transparently handle reading and writing the value.

## Expected Behavior

- Each of the standard UUID format wrapper types (braced, hyphenated, simple, URN) should be usable directly as an entity model field type
- Inserting a record whose fields use these formatted UUID types should work without any special handling
- Retrieving a record from the database should return the correct formatted UUID values, matching what was inserted
- Array columns (for databases that support them) should also support vectors of these formatted UUID types as entity field types

## Why This Matters

This allows developers to model their data in terms of how they actually use UUIDs, without needing conversion boilerplate after every query. If an application always works with hyphenated UUIDs, it should be possible to declare that in the entity model and have the ORM enforce it transparently.
