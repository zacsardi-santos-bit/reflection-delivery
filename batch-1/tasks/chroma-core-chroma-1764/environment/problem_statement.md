## Description

The log service currently only supports pushing records into a collection's log. There is no way to retrieve those records back out. Consumers need the ability to read back stored log records starting from a specific position and up to a configurable batch size, so they can process logs incrementally.

Additionally, neither the push nor pull operations validate the collection identifier before proceeding. When an invalid identifier is provided, the service produces confusing or opaque errors instead of a clear, actionable response.

## Expected Behavior

- The log service should expose a way to pull stored records for a given collection, accepting a starting log position and a maximum number of records to return.
- Pulling from position 0 should return records from the beginning of the log.
- Pulling from a specific position should return only records at or after that position, up to the batch size limit.
- Both push and pull operations should validate the collection identifier. When an invalid identifier is provided, the service should immediately return a descriptive error indicating the identifier is invalid.

## Why This Matters

Without the ability to read back records, the log service is write-only and cannot support consumers that need to process previously stored data. The missing validation also makes it harder to debug issues caused by malformed identifiers, since errors are not surfaced clearly at the service boundary.
