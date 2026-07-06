## Description

The DSv2 streaming connector for Delta Lake needs support for Change Data Capture (CDC) reads. Currently, the internal file tracking structure used by the streaming reader has no concept of CDC file entries — it can only distinguish between positional marker entries (sentinels) and plain data file entries. This makes it impossible to carry CDC-specific metadata such as the type of change (insert, update, delete) and the commit timestamp through the streaming pipeline.

Additionally, creating sentinel entries currently requires passing null to the class constructor, which is error-prone and does not clearly express intent.

## Expected Behavior

- A new type of file entry should wrap a regular file with CDC metadata (change type and commit timestamp), enabling the streaming path to carry that metadata alongside the file reference.
- The internal file representation should use explicit, named factory methods for the three distinct entry types: positional markers (sentinels), regular data file entries, and CDC data file entries.
- Sentinel entries should not expose a file size — attempting to access the size of a sentinel should result in an error.
- The string representation of each entry type should clearly identify which kind of entry it is and its key fields.

## Why This Matters

Without this foundation, the DSv2 streaming connector cannot support CDC-enabled Delta tables. Consumers that need to stream row-level change data (inserts, updates, deletes) through the DSv2 API have no way to receive or interpret that information today. These changes provide the building blocks needed to match the CDC streaming behavior available through the DSv1 path.
