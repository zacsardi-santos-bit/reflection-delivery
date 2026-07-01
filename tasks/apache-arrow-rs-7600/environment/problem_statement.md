## Description

When writing encrypted Parquet files programmatically, there is currently no way to embed key identification metadata alongside the encryption keys for the file footer or individual columns. This means that consumers using a key-retrieval-service pattern — where decryption keys are looked up by name or identifier rather than embedded directly in the reader configuration — cannot successfully read files written by this library.

Specifically, while the read path already supports key retrievers that identify keys by metadata, the write path has no corresponding way to attach that metadata to the footer key or column keys. This breaks the write-then-read roundtrip for any application that relies on a key management system.

## Expected Behavior

- When writing an encrypted Parquet file, it should be possible to attach a key metadata identifier to the footer encryption key.
- When writing an encrypted Parquet file, it should be possible to specify both the encryption key and its metadata identifier for individual columns in a single operation.
- A file written with plaintext footer and key metadata identifiers should be fully readable by a key retriever that can resolve those identifiers to the correct key bytes.
- When reading such a file with an incorrect key (the key retriever returns wrong bytes for a metadata identifier), the reader should return an informative error indicating that the footer signature verification failed.

## Why This Matters

Applications integrating with key management services need to be able to write Parquet files that identify which keys were used, so that readers can retrieve the correct keys automatically. Without this, there is no practical way to produce encrypted Parquet files that are compatible with key-retriever-based decryption.
