Implement support for optimistic concurrency control in the bulk indexer of the go-elasticsearch client. Update the BulkIndexerItem struct to include optional fields for sequence number and primary term, and ensure these fields are correctly serialized in the metadata header when specified.

*   Update the BulkIndexerItem struct in `esutil/bulk_indexer.go`:
    *   Add two new optional pointer fields:
        *   `IfSeqNo` of type `*int64`
        *   `IfPrimaryTerm` of type `*int64`

*   Modify the `marshallMeta` method in `esutil/bulk_indexer.go`:
    *   Ensure the method serializes the item metadata into a JSON action header.
    *   Include the fields `if_seq_no` and `if_primary_term` with their integer values in the metadata when:
        *   `DocumentID` is non-empty.
        *   Both `IfSeqNo` and `IfPrimaryTerm` are set (non-nil).
    *   Ensure the serialized output for an 'index' action with `DocumentID` '1', `IfSeqNo` 45, and `IfPrimaryTerm` 67 is: `{"index":{"_id":"1","if_seq_no":45,"if_primary_term":67}}` followed by a newline.
    *   Omit the `if_seq_no` and `if_primary_term` fields if:
        *   Either `IfSeqNo` or `IfPrimaryTerm` is nil.
        *   `DocumentID` is empty.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.