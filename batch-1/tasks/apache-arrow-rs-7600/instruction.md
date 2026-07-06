Implement methods to support embedding key metadata identifiers in encrypted Parquet files. This will enable key-retriever-based decryption by associating metadata with encryption keys for the file footer and individual columns.

*   Update the `FileEncryptionProperties` builder to support key metadata:
    *   Implement the method `with_footer_key_metadata(self, key_metadata: Vec<u8>) -> Self` in `parquet/src/encryption/encrypt.rs`.
        *   Attach key metadata (a byte sequence) to the footer encryption key.
        *   Ensure this metadata is embedded in the Parquet file footer.
    *   Implement the method `with_column_key_and_metadata(self, column_name: &str, key: Vec<u8>, key_metadata: Vec<u8>) -> Self` in `parquet/src/encryption/encrypt.rs`.
        *   Set both the encryption key and key metadata for a specified column in a single operation.
        *   Accept the column name, key bytes, and metadata bytes as arguments.

*   Ensure compatibility with key retrievers:
    *   Verify that a Parquet file written with a plaintext footer and key metadata identifiers is readable by a key retriever that resolves these identifiers to the correct key bytes.
    *   Handle incorrect key retrieval:
        *   If the key retriever provides an incorrect key for the footer metadata identifier, ensure the reader fails with an error message starting with: "Parquet error: Footer signature verification failed. Computed: [".

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.