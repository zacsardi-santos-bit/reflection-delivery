## Description

The SSL buffer-freeing mechanism does not correctly handle pipelined connections. When an SSL connection is using a pipeline-capable cipher, it is possible for one pipelined record to be fully received and read by the application while a second pipelined record is only partially available in the read buffer. In this situation, the library incorrectly permits buffer freeing even though there is still pending data that those buffers are needed for.

This can lead to incorrect memory management behavior. The library should refuse to free SSL buffers whenever any pending or partially-received data remains — including partial pipeline records that haven't been fully delivered yet.

## Expected Behavior

- The library must refuse to free SSL connection buffers whenever there is any unread data remaining, regardless of whether it was fully or partially received.
- This includes scenarios where only a record header was received, where only part of a record body was received, or where the application has read only some of the available data.
- When pipelining is in use, if a second pipelined record is partially received, the library must still refuse to free the buffers — even if the application has successfully read the first complete pipelined record.
- A helper for loading and initializing the pipeline-capable test engine should be available in the SSL test helpers so it can be reused across multiple test files.

## Why This Matters

Incorrect buffer-free decisions in pipelined SSL connections can cause data corruption or use-after-free errors in applications that rely on SSL pipelining for performance. Ensuring that buffer state is accurately checked across all pipelined records prevents these potential issues.
