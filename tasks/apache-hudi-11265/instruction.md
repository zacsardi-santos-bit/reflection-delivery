Implement the `convert` method in `MercifulJsonConverter.java` to handle JSON-to-Avro conversion for various Avro logical types, ensuring proper encoding and validation. Define a specific exception for conversion errors and add a utility method for schema loading in test utilities.

Requirements:

*   Implement `MercifulJsonConverter.convert(String json, Schema schema)` to handle:
    *   **Decimal** types:
        *   Accept string numbers or numeric values.
        *   Return Avro bytes or fixed representations.
        *   Throw `HoodieJsonToAvroConversionException` for precision/scale mismatches, non-numeric strings, or invalid raw byte arrays.
    *   **Duration** types:
        *   Accept a JSON array of three integers [months, days, milliseconds].
        *   Return a 12-byte `GenericFixed` value.
        *   Throw `HoodieJsonToAvroConversionException` for invalid list sizes, non-list inputs, or incorrect schema sizes.
    *   **Date** types:
        *   Accept epoch-day integers, ISO date strings, or their string equivalents.
        *   Return days since Unix epoch as an int.
        *   Throw `HoodieJsonToAvroConversionException` for invalid types or malformed dates.
    *   **Local-timestamp** types:
        *   Accept ISO local datetime strings or numeric long values.
        *   Return milliseconds or microseconds since epoch.
        *   Throw `HoodieJsonToAvroConversionException` for timezone markers or malformed inputs.
    *   **Timestamp** types:
        *   Accept ISO datetime strings with 'Z' or numeric long values.
        *   Return milliseconds or microseconds since Unix epoch.
        *   Throw `HoodieJsonToAvroConversionException` for invalid formats or missing components.
    *   **Time** types:
        *   Accept int/long values or time strings in 'HH:MM:SS[.fractional]' format.
        *   Return milliseconds or microseconds since midnight.
        *   Throw `HoodieJsonToAvroConversionException` for malformed strings or out-of-range values.
    *   **UUID** types:
        *   Accept any string value and return unchanged.

*   Define `HoodieJsonToAvroConversionException`:
    *   Public nested class within `MercifulJsonConverter`.
    *   Thrown for all conversion failures.

*   Implement `SchemaTestUtil.getSchemaFromResourceFilePath(String filePath)`:
    *   Parse and return an Avro Schema from a classpath resource.
    *   Located in `hudi-common/src/test/java/org/apache/hudi/common/testutils/SchemaTestUtil.java`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.