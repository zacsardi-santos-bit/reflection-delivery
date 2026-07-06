## Description

Flink's compressed operator state checkpoints are not correctly restored when states need to be read from specific byte offsets within a shared compressed stream. Two related bugs prevent this from working:

1. When the output side of the compressed stream reports its current write position, the internal compression buffer has not been flushed, so the reported position does not correspond to any meaningful boundary in the underlying raw byte stream. Any attempt to later seek to that position and read back the data will return incorrect results.

2. When the input side of the compressed stream seeks to a new position in the raw byte stream, it does not discard decompressed bytes that are already buffered. These stale bytes are then returned by subsequent reads, corrupting the data being restored.

## Expected Behavior

- The position reported by the compressed output stream should always reflect a fully flushed state, so it can be used as a reliable seek target during restore.
- Seeking within a compressed input stream should clear any buffered decompressed data before repositioning, so that subsequent reads return the correct content for the new position.
- Operator state snapshot and restore should work correctly both with and without compression, including for empty states, multiple merged handles, and repartitioned state across different parallelism levels.

## Why This Matters

These bugs cause silent data corruption during checkpoint restore when compression is enabled: restored operator state may contain wrong or missing values. This affects all Flink jobs that use compressed state checkpoints and need to read back individual state entries by their stored offsets (which is the normal restore path for partitioned list and broadcast states).
