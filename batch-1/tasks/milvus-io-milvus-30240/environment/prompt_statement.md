I'm in the data node write buffer in Milvus and the segment flush lifecycle is missing an intermediate step. Right now when a flush gets triggered, segments jump straight into the flushing state, skipping any sealed state in between, so there's no clean way to tell apart segments that were just requested for flush versus ones that are already being actively flushed. I want to fix that.

What I need is a new sync policy that's aware of the sealed state. When a flush is requested, segments should first move into a sealed intermediate state, and then this policy should be the thing that watches for sealed segments, advances them to the flushing state as part of the sync pass, and returns their IDs for syncing, all in one step. So detect sealed, transition to flushing, schedule for sync, done together.

Then swap it in: the old policy that only looked for segments already in the flushing state should be replaced by this new sealed-aware policy in the write buffer's default sync policy config, and don't forget the alternative storage variant of that default config too, it needs the same treatment.

The point here is observability and correct state transitions, without a real intermediate state we can't track where a segment sits between active and actively flushing, so this reduces the risk of wrong behavior as segments move through the flush stages.
