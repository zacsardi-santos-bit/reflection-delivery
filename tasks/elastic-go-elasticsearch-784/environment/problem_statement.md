# Add Support for Optimistic Concurrency Control in Bulk Indexer Items

## Description

The bulk indexer does not currently support optimistic concurrency control parameters on individual items. When indexing documents in bulk, it is sometimes necessary to conditionally apply a write only if the document is at a specific version — identified by its sequence number and primary term. Without these parameters, bulk index operations cannot take advantage of the version-based conflict detection that the underlying search engine supports.

## Expected Behavior

- A bulk indexer item should accept a sequence number and a primary term as optional pointer values.
- When both values are provided along with a document ID, they must be serialized into the item's metadata header as part of the JSON action object.
- The serialized output should include both the sequence number field and the primary term field with their correct integer values.
- If either value is absent, or if no document ID is provided, the fields should be omitted from the metadata.

## Why This Matters

Optimistic concurrency control is an important feature for safely updating documents in a distributed system. Without support for these parameters in the bulk indexer, developers are forced to use single-document APIs for conditional writes, losing the throughput benefits of batched operations. Adding these fields enables conflict-safe bulk indexing workflows.
