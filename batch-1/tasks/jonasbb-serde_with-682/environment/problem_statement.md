## Description

When using the serialization transformation macro on a struct field that also has an explicit JSON schema type annotation, the code fails to compile. The macro generates its own schema annotation for the field without checking whether one already exists, leading to a conflict that prevents the project from building.

## Example Scenario

A user has a struct field that they want serialized as a string for transport (using the transformation macro) but documented as an integer in the generated JSON schema (using an explicit schema-type annotation). Currently, combining these two annotations causes a compilation error. There is no way to customize the JSON schema for a field independently from how that field is serialized.

Conditional schema annotations (those guarded by compile-time conditions) should also work correctly: if the user-specified annotation is disabled at compile time, the macro should fall back to its automatic schema generation; if the annotation is enabled, the macro should defer to it.

## Expected Behavior

- A field with both a serialization transformation and an explicit schema type annotation must compile successfully
- The generated schema must use the user-specified type, not the transformation's automatic type
- Conditionally-enabled schema annotations must behave the same as unconditional ones (macro defers to the user annotation)
- Conditionally-disabled schema annotations must be treated as absent (macro generates its own schema automatically)

## Why This Matters

Users who need to document their API schemas accurately while also customizing serialization behavior are currently blocked. Common cases include fields that are integers in the API contract but serialized as strings over the wire, or fields where the public schema should differ from the internal representation.
