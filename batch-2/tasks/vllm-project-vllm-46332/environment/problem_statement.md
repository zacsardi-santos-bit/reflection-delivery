## Description

When running prefill/decode disaggregation with different numbers of GPU workers on each side (heterogeneous tensor parallelism), the system can incorrectly release KV cache blocks too early. If the decode side has more workers than the prefill side, multiple decode workers will each send a "done" signal for the same cached data. The system currently marks a transfer complete after the very first such signal arrives, even though other decode workers are still using those blocks. This leads to memory corruption or silent data races.

## Expected Behavior

- The prefill side should wait for all decode workers to signal completion before freeing a transfer's blocks. The number of expected signals depends on the ratio of decode-to-prefill worker counts.
- Decode workers should include their worker count in the completion notification so the prefill side can calculate how many acknowledgments to expect.
- Notification messages that carry additional fields (such as worker count) should be properly serialized and included in the sent payload.
- Acknowledgment tracking should handle duplicate signals after a transfer is already complete — subsequent signals for a finished transfer must be ignored rather than triggering another completion event.
- Configurations where KV attention heads would need to be split in an unsupported way between heterogeneous worker counts should be detected early and rejected with a clear error.
- Worker count mapping between the two sides should correctly compute which remote worker rank corresponds to each local worker rank, raising an error when the counts are not integer multiples of each other.

## Why This Matters

Without this fix, deploying prefill/decode disaggregation with an asymmetric number of GPU workers (e.g., 4 prefill GPUs and 8 decode GPUs) risks silently freeing shared memory blocks while they are still in use, causing unpredictable behavior and potential crashes. This change makes heterogeneous tensor-parallel prefill/decode disaggregation safe to use.
