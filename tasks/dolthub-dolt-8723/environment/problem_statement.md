## Description

Dolt's JSON storage system is unable to correctly handle JSON documents that contain very large string values (multiple megabytes). When such a document is inserted into a JSON column and then read back, the value comes back truncated or the operation fails entirely.

The root cause is that the internal indexed storage format for JSON documents imposes chunk size limits, and strings that exceed those limits cannot be split across chunks without causing corruption or breaking compatibility with older clients. There is currently no fallback — documents with oversized string values are not handled gracefully.

## Expected Behavior

- Inserting a JSON document with a multi-megabyte string value (either as a value or as a key) should succeed without errors.
- Reading the value back from the JSON column should return the full, untruncated content — not a shortened version.
- Documents that contain strings too large for the indexed storage format should automatically be stored in a simpler, compatible format, without requiring any change to the user's query.
- Querying the length of a large JSON string value via a JSON path expression should return the correct full length.

## Why This Matters

Users who store large blobs of text inside JSON columns — such as encoded content, large payloads, or machine-generated strings — currently experience silent data corruption or errors. This is a data integrity issue that can go undetected. Fixing this ensures that JSON columns in Dolt faithfully store and return whatever content the user inserts, regardless of string length.
