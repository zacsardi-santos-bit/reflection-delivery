Implement enhancements to the data node's write buffer to improve visibility into memory usage. Update the internal buffering routine to return the actual memory size of buffered data and ensure the metric tracking this data is accurate and useful for monitoring.

*   Update the `Buffer` method in `internal/datanode/writebuffer/insert_buffer.go`:
    *   Modify the method signature to return three values: `([]storage.FieldData, int64, error)`.
    *   Ensure the method returns the primary key field data slice, the total memory size in bytes of all buffered insert data, and any error encountered.
    *   Ensure the memory size is calculated as the sum of the memory size of each insert message's full data, not just the primary key fields.
    *   Return a memory size of 0 in case of any error.

*   Ensure correct memory size calculations:
    *   For a dataset of 10 rows with 128-dimensional float vectors, the total memory size must be 5364 bytes.
  
*   Update the `DataNodeFlowGraphBufferDataSize` metric in `pkg/metrics/datanode_metrics.go`:
    *   Increment the metric by the memory size of buffered insert data when `BufferData` is called with insert and delete messages.
    *   Ensure the metric value after buffering 10 rows of 128-dimensional vectors is 5524.
    *   Use node ID and collection ID as label values, formatted as strings, in that order.

*   Manage metric updates on data flush:
    *   When `BufferData` triggers an automatic sync that flushes all buffered segments, update the metric to reflect the remaining buffer size.
    *   Ensure the metric value is 0 when the buffer is fully flushed.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.