## Description

When a consumer group rebalance times out, the group coordinator incorrectly evicts static (persistent) group members who have not yet rejoined — even if those members are still within their session timeout window and are theoretically still alive. This is incorrect behavior: static members are specifically designed to tolerate temporary disruptions without losing their group membership. Only dynamic (non-static) members should be removed when the rebalance window expires.

There are also related issues:

1. **Leader re-election is missing**: If the current group leader fails to rejoin before the rebalance deadline but other members have rejoined, the group should elect a new leader from the joined members and proceed. Currently it gets stuck.

2. **Incorrect heartbeat response for missed rebalance**: A static member that misses a rebalance (doesn't rejoin in time) currently receives an "unknown member" error on heartbeat. It should instead receive an "illegal generation" error, which correctly signals that the group has moved to a new generation without it.

## Expected Behavior

- Static members who don't rejoin within the rebalance timeout should remain registered in the group (not be evicted), because their session timeout hasn't expired yet.
- If the group leader fails to rejoin but other members have, one of the rejoined members should be elected as the new leader and the rebalance should complete normally.
- If no member has rejoined at all (only non-rejoining static members remain), the group should reschedule a new rebalance delay until session timeouts expire.
- When the rebalance completes with some static members absent, those members' instance identifiers should still appear in the leader's join result.
- Static members that missed the rebalance must receive an "illegal generation" response on heartbeat (not "unknown member").

## Why This Matters

Static group membership exists to allow consumer group members to survive brief restarts or disruptions without triggering a full rebalance. Incorrectly evicting these members during a rebalance defeats the purpose of the feature and causes unnecessary instability in consumer groups.
