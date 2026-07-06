## Description

When an agent spawns a subagent in a new thread, analytics events fired by that subagent — such as compaction events — are silently dropped. This happens because the subagent thread has no connection record of its own, so the analytics reducer cannot resolve the client metadata needed to emit the event.

## Expected Behavior

- When a subagent thread is created through a parent thread spawn, it should automatically inherit the parent thread's connection context.
- Analytics events generated within the subagent thread — including compaction events — should be correctly attributed to the parent client connection.
- The parent thread relationship should be properly recorded in the emitted analytics data, so downstream consumers can trace which client and thread originated the subagent activity.

## Why This Matters

Without this fix, any analytics event (e.g. context compaction) that occurs inside a spawned subagent thread is silently discarded. This creates a blind spot in analytics for multi-agent workflows: operators cannot see what the subagent was doing, which client it belonged to, or how it relates to the parent thread. Ensuring proper connection inheritance closes this gap and makes subagent activity fully observable through analytics.
