## Description

The CloudTrail log unmarshaler currently only supports batch processing — it reads the entire input and returns all records at once. This is problematic for integrations that need to process logs incrementally, track progress across restarts, or resume from a known checkpoint without reprocessing already-handled records.

We need to add streaming support to the CloudTrail log unmarshaler so that consumers can:
- Decode one or more records at a time (rather than all records at once)
- Track exactly how many records have been processed
- Start from a specified position to skip records that were already processed

## Expected Behavior

- The CloudTrail log component should expose a streaming decoder interface that returns records incrementally based on a configurable batch size.
- When given a starting position (offset), the decoder should skip that many already-processed records before beginning to return results.
- After all records have been returned, the decoder should signal end-of-stream cleanly, and any further requests should also return end-of-stream.
- The decoder should expose a method for callers to query the current progress position at any time.
- For CloudWatch subscription filter and digest log formats — which do not support partial replay — the decoder should immediately signal end-of-stream when given a non-zero starting position, and should report the full byte count consumed so that upstream components can maintain accurate state.

## Why This Matters

Without streaming support, a pipeline that restarts mid-way through a large CloudTrail log file would need to reprocess all previously seen records. With an offset-aware streaming decoder, the pipeline can resume exactly where it left off, making CloudTrail log processing more reliable and efficient for high-volume environments.
