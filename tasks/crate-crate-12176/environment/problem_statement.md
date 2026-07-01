## Description

When object types contain fields with names that include special characters — such as spaces, parentheses, brackets, or embedded quotation marks — the type signature serialization and parsing system fails to correctly round-trip those names. As a result, the system cannot reconstruct the original type from its stored signature when field names include these characters.

## Expected Behavior

- Object field names containing spaces, brackets, or other special characters should be properly escaped in the serialized type signature representation (using double-quote wrapping and backslash escaping for embedded quotes).
- The type signature parser should be able to read back these escaped names and correctly reconstruct the original type.
- Field names that do not contain special characters should continue to be serialized and parsed as before.
- A dedicated type signature parser should be introduced as a standalone component, separate from the type definition class, making the parsing logic more maintainable and independently testable.
- Named type signature parameters should expose a way to retrieve the decoded (unescaped) field name, without the surrounding quotes or escape sequences.

## Why This Matters

Users who create object columns with field names containing spaces, parentheses, or other special characters currently encounter failures in type resolution and type signature handling. Fixing this ensures that arbitrary field names are supported in object types and that the type system remains consistent when persisting and restoring type information.
