Implement a robust serialization and parsing system for type signatures that correctly handles field names containing special characters. Ensure that these names are properly escaped during serialization and accurately reconstructed during parsing. Refactor the parsing logic into a standalone class for improved maintainability and testability.

*   Create a new class `TypeSignatureParser` in the `io.crate.signatures` package.
    *   Implement a public static method `parse(String signature)` that returns a `TypeSignature` object.
    *   Ensure this method supports parsing of primitive, array, object, record, text-with-length, and numeric-with-precision/scale type signatures.
    *   Handle double-quoted parameter names, where names are enclosed in double quotes and internal double quotes are backslash-escaped.

*   Update `ParameterTypeSignature` class in `server/src/main/java/io/crate/types/ParameterTypeSignature.java`.
    *   Implement a public method `unescapedParameterName()` that returns the decoded field name, stripping surrounding double quotes and converting backslash-escaped double quotes to literal double quotes.

*   Modify `ObjectType.getTypeSignature().toString()` to:
    *   Serialize field names with special characters using double quotes.
    *   Escape internal double-quote characters in field names with a backslash.

*   Ensure `TypeSignatureParser.parse()` can correctly round-trip object type signatures with quoted parameter names, maintaining type equality after parsing and type creation.

*   Update the existing `TypeSignature.parseTypeSignature(String)` method to handle quoted parameter names, including backslash-escaped internal double quotes.

*   Declare `IntegerLiteralTypeSignature` public in `io.crate.types` package.
    *   Ensure its constructor accepting a single integer value is public for external instantiation.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.