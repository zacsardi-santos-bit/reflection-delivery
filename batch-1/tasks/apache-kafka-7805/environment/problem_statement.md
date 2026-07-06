## Description

When an older Kafka broker is serving clients and a partition reassignment is in progress, the metadata responses it sends include leader epoch information that is not reliably maintained during the reassignment. A client that caches these unreliable epochs and then forwards them in subsequent Fetch, ListOffsets, or similar requests can receive errors rejecting those requests as having a "fenced" leader epoch. This can completely block reads and writes for the entire duration of the partition reassignment.

There are two related problems:

1. Metadata responses received via the legacy wire format cannot be assumed to carry reliable leader epochs. The client currently does not distinguish between a response it can trust and one it cannot, so it may cache bad epoch values.

2. When a metadata update arrives with an older epoch than what is already cached for a partition (stale metadata), the client currently overwrites the good cached data with the stale data, which can cause further confusion.

## Expected Behavior

- When a metadata response is known to come from a source that cannot guarantee accurate epoch propagation, the client should discard the epoch information rather than caching an incorrect value.
- When a newer metadata update carries a lower epoch than what is already stored for a partition, the stale update should be ignored and the previously cached partition information (including ISR and epoch) should be preserved.
- Producers and consumers should continue to function without interruption during partition reassignments.

## Why This Matters

Partition reassignments are a routine operational activity. Blocking all consumer and producer activity for the full duration of a reassignment is a severe availability impact that should be avoided.
