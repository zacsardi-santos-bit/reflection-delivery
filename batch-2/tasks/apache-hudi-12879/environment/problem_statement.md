## Description

When streaming data from message queues into a Hudi table, decimal-typed fields are often serialized as raw bytes (specifically, the unscaled integer representation encoded in Base64). The current ingestion pipeline cannot correctly handle these fields during JSON-to-Avro conversion: it fails to decode the Base64 byte payloads into proper decimal values, and it has no way to determine whether a schema contains decimal fields that need special treatment.

## Expected Behavior

- A utility method should be available to check whether a given Avro schema contains any decimal logical type field, regardless of whether those decimals appear at the top level, as map value types, or as array element types.
- A utility method should be available to convert the raw unscaled bytes of a decimal value into the fixed-width Avro representation, so that byte-encoded decimals can be written into fixed-type decimal fields correctly.
- The JSON-to-Avro conversion layer should correctly handle records where decimal fields arrive as Base64-encoded strings. Whether the target schema expects a byte-based decimal or a fixed-width decimal, the converter must produce the correctly-typed and correctly-valued Avro field.
- Test data generation utilities should support producing records that conform to a schema with multiple decimal fields, with values encoded in the Base64 byte format that reflects real message queue payloads.

## Why This Matters

Data pipelines ingesting from message queues frequently encounter decimal values encoded as byte arrays. Without proper handling, decimal values are silently lost or the ingestion fails entirely. This fix ensures that decimal fields survive the round-trip from source message to stored Avro record with their values intact.
