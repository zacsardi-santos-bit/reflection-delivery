## Description

Ozone datanodes replicate container data across multiple replicas, and when replicas drift out of sync — due to corruption, missed writes, or partial data — there needs to be a way to identify exactly what each datanode is missing or has corrupted compared to its peers. Currently the container comparison method is a stub that always returns an empty result, making it impossible to act on checksum tree differences.

## Expected Behavior

- When a datanode compares its local container checksum tree against a peer's, it should receive a detailed report listing which entire blocks are missing, which specific chunks within blocks are absent, and which chunks are corrupted (present locally but with a bad checksum while the peer's copy is healthy).
- When the peer's tree is the one with missing data (rather than ours), our diff report should be empty — we only report what we locally need to repair.
- Blocks that have been deleted — whether on our side or the peer's side — should be excluded from the diff report, since their absence is intentional.
- If the comparison fails (for example, no local checksum file exists, or the container identifiers don't match), the operation should fail with a clear error.

## Metrics

The comparison operation should be tracked with metrics:
- Latency of each diff operation
- Count of diffs that require repair
- Count of diffs that require no repair
- Count of diff failures

## Why This Matters

Without this, the container reconciliation pipeline cannot determine what data to copy from a healthy peer to fix a degraded replica. This is a critical building block for automated container repair in Ozone.
