I'm working on a Rust port of a package manager and running into deserialization failures when fetching package metadata from the npm registry.

*   The PackageVersion struct must include a new optional field named 'deprecated' of type Option<String>.

*   When the 'deprecated' field is absent from JSON or is explicitly null, it must deserialize as None.

*   When the 'deprecated' field is the boolean value false, it must deserialize as None (not deprecated).

*   When the 'deprecated' field is the boolean value true, it must deserialize as Some("") — an empty string indicating the package is deprecated without a recorded reason.

*   When the 'deprecated' field is a non-empty string (e.g., a deprecation message), it must deserialize as Some(that_string), preserving the original message.

*   The deprecated field must not affect serialization when it is None (it should be omitted from serialized output).


*   Interface details: Type: Struct field
Name: deprecated
Location: pacquet/crates/registry/src/package_version.rs
Description: New public optional field on the PackageVersion struct. Must be of type Option<String> and use a custom deserializer that normalizes the npm registry's wire format: boolean false → None, boolean true → Some(""), string → Some(string), absent/null → None. Should be skipped during serialization when None.
Signature: pub deprecated: Option<String>


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.