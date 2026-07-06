Implement methods and utilities to handle decimal fields in a Hudi data ingestion pipeline. Ensure that decimal fields, serialized as Base64-encoded byte arrays, are correctly processed during JSON-to-Avro conversion and that schemas with decimal fields are identified.

*   Implement `HoodieAvroUtils.convertBytesToFixed`:
    *   Accept a byte array and a fixed-type Avro Schema with a decimal logical type.
    *   Return a `GenericData.Fixed` object that matches the original BigDecimal when decoded.

*   Implement `HoodieAvroUtils.hasDecimalField`:
    *   Return `true` if the schema contains any decimal logical type field, including nested in maps or arrays.
    *   Return `false` if no decimal logical type is present.

*   Ensure `HoodieAvroUtils.hasDecimalField` returns:
    *   `true` for `HoodieTestDataGenerator.AVRO_SCHEMA` and `HoodieTestDataGenerator.AVRO_TRIP_ENCODED_DECIMAL_SCHEMA`.
    *   `false` for schemas with only non-decimal fields.

*   Update `MercifulJsonConverter.convert`:
    *   Correctly process JSON records with Base64-encoded decimal fields.
    *   Convert to `ByteBuffer` or `GenericData.Fixed` as required by the target schema.

*   Update `HoodieTestDataGenerator`:
    *   Define `TRIP_ENCODED_DECIMAL_SCHEMA` as a public static final String with specified fields and decimal types.
    *   Define `AVRO_TRIP_ENCODED_DECIMAL_SCHEMA` as a public static final Schema parsed from `TRIP_ENCODED_DECIMAL_SCHEMA`.
    *   Implement `generatePayloadForTripEncodedDecimalSchema` to produce records with Base64-encoded decimal fields.
    *   Implement `generateRecordForTripEncodedDecimalSchema` to create `GenericRecord` with specified decimal fields encoded as Base64.

*   Ensure sample record generation dispatches to `generatePayloadForTripEncodedDecimalSchema` when using `TRIP_ENCODED_DECIMAL_SCHEMA`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.