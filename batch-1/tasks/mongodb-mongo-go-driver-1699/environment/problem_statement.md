## Description

Working with large BSON documents in the Go driver can be extremely expensive when those documents need to be converted to strings for logging or debugging. Currently, converting a document to its string representation always produces the full output — even when only a short preview is needed. For documents with hundreds of thousands of keys, multi-megabyte string values, or massive arrays, this creates noticeable performance problems: significant CPU time and memory is consumed generating a complete string that will only ever be logged as the first few hundred bytes.

## Expected Behavior

- It should be possible to convert a BSON document, array, or individual value to a string representation that is capped at a specified byte length. Once the output reaches the limit, generation should stop rather than continuing to build the full string.
- For non-positive limits, the function should return an empty string.
- When the full representation fits within the limit, the complete representation should be returned unchanged.
- A utility function for truncating arbitrary strings to a byte-width limit should exist in a shared internal package. It must handle multi-byte Unicode characters correctly — never splitting a character — and must not append any trailing ellipsis or suffix. The limit parameter should use a signed integer type.
- The logging layer should expose a function that formats a raw BSON document into a bounded string representation, suitable for use in command logging.

## Why This Matters

Logging commands that include large documents (e.g. bulk inserts, large query results) currently forces the full document to be stringified even when the log output is limited. This is wasteful and can cause latency spikes in high-throughput scenarios. Bounded stringification allows the driver to produce log-friendly document previews efficiently, without generating the full representation first.
