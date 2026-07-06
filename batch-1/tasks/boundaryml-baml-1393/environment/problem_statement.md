## Description

The streaming partial deserializer breaks when processing output types that have array fields typed as a union of different possible object shapes. When an array can hold items from several different class types, the deserializer should look at the actual type of each item to figure out which fields are required and which need to be filled in — but instead it is consulting the field's declared union specification, which is not a concrete class and cannot be used to look up field definitions. This causes the deserializer to fail entirely for those items or produce incorrect results.

## Expected Behavior

- When an output class has one or more array fields whose elements can be any of several different object types, each element should be deserialized according to its own concrete type's field schema.
- Fields present in the input should be preserved. Fields absent from the input should be filled in with null according to the resolved class type, not the union type.
- Mixed-type value arrays (e.g., arrays that may contain strings, integers, or floats) nested inside these objects should also be preserved correctly.
- All items in all arrays should appear in the output, in order, with correct structure.

## Why This Matters

Users who define BAML schemas with union types in array fields get incorrect or failed deserialization during streaming, even when the underlying JSON is perfectly valid and complete. This is a correctness bug that makes the streaming deserializer unreliable for any schema that uses union types in collections.
