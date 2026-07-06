## Description

The write buffer in the data node currently lacks visibility into how much data is accumulating in memory before being flushed to storage. There is no reliable metric tracking buffer data size per collection per node, making it hard for operators to monitor memory pressure or diagnose performance issues in the ingestion pipeline.

Additionally, the internal routine responsible for buffering incoming records does not report the actual memory footprint of the data it processes. This means that callers of that routine cannot use the real buffer size to inform decisions about resource management or metric reporting.

## Expected Behavior

- The routine that buffers incoming insert records should return the total memory size of the data it buffered, in addition to the existing field data and error return values.
- After data is buffered, a per-node, per-collection metric should be updated to reflect the total amount of data currently held in the write buffer.
- When all buffered data for a segment is flushed or synced, the metric should correctly reflect that the buffer is now empty (i.e., the tracked size drops to zero).
- The memory size should be computed from the full insert data, not just from the primary key fields.

## Why This Matters

Without this visibility, operators cannot observe how much data is waiting in memory across collections, making it difficult to tune flush policies or detect situations where buffers are growing unexpectedly. Tracking this via a metric enables real-time observability and better operational control.
