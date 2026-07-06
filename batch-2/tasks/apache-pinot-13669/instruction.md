Implement a method to recursively convert logical type fields within Avro records, ensuring that nested structures such as arrays, maps, and records are properly processed. This will allow logical types at any depth in the schema to be resolved to their corresponding Java types.

*   Implement the `convertLogicalType` method in `AvroSchemaUtil.java` with the following signature:
    *   `public static GenericRecord convertLogicalType(GenericRecord record)`
    *   Ensure it returns a new `GenericRecord` with all logical type fields converted.

*   Conversion Requirements:
    *   Convert UUID logical types (Avro string with logicalType 'uuid') to `java.util.UUID`.
    *   Convert timestamp-millis logical types (Avro long with logicalType 'timestamp-millis') to `java.time.Instant`.
    *   Convert decimal logical types (Avro bytes with logicalType 'decimal') to `java.math.BigDecimal` using the scale from the schema.
    
*   Handle Nested Structures:
    *   For ARRAY fields:
        *   Convert each element if the element type has a logical type.
        *   Recursively process each element if the element type is a RECORD.
    *   For MAP fields:
        *   Convert each value if the value type has a logical type.
        *   Recursively process each value if the value type is a RECORD.

*   General Handling:
    *   Pass through any field without a logical type and not part of an ARRAY or MAP unchanged.
    *   Handle union schemas by selecting the sub-schema with a logical type when present.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.