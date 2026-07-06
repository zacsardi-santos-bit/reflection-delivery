## Description

The BookKeeper client currently has no way to read multiple ledger entries from a bookie in a single network request. Every entry requires its own round trip, which becomes a serious performance bottleneck when a client needs to read a long sequence of entries — for example, during ledger recovery or replay.

We need a batch read operation that lets a client request a range of consecutive entries from a bookie in one call, receiving all of them together when the response arrives.

## Expected Behavior

- A client can issue a single batch read request specifying the starting entry, a maximum number of entries, and a maximum total response size.
- The bookie returns as many consecutive entries as possible, stopping when it hits a gap in the entry sequence (a missing entry), reaches the count limit, or would exceed the size limit.
- The response includes the complete entry data for each returned entry in order.
- If entries exist starting from the requested position, the operation succeeds and delivers the available entries.
- If the very first requested entry is missing, the operation reports an appropriate "no such entry" error condition.
- If entries exist up to some point and then there is a gap, the operation succeeds and delivers only the entries before the gap.
- The size limit accounts for response framing overhead in addition to raw entry data.

## Why This Matters

Fetching entries one at a time is inefficient for readers that need to scan or replay a ledger. A batch read interface reduces the number of network round trips needed, significantly improving throughput for sequential read workloads. This also enables more efficient recovery and replication scenarios.
