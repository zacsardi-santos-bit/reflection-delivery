## Description

The memory consolidation pipeline currently selects stage-1 outputs for phase-2 consolidation based only on how recently they were updated. It does not consider whether those memories have actually been accessed or used within a meaningful time window. This means long-stale memories can be included in consolidation even though they are no longer relevant to current usage patterns.

We need to add a configurable staleness threshold so that the selection query can filter out memories that haven't been used within a certain number of days. Memories that have never been used should still be eligible, using their creation date as a fallback.

## Expected Behavior

- The memory selection function must accept a maximum-unused-days parameter that controls the recency window for inclusion.
- Memories with a recorded last-usage timestamp that falls outside the configured window should be excluded.
- Memories with no last-usage record (never used) should remain eligible based on when they were generated, provided that date is within the window.

## Related Fix

A test that validates reclaiming a stale consolidation lock was incorrectly asserting only one specific outcome. In fast environments the consolidation job can finish before the follow-up lock attempt, which is equally valid. The test should accept both "job still running" and "job already completed with nothing left to process" as correct outcomes.

## Why This Matters

Without a recency filter, the consolidation step may process memories that are no longer relevant, wasting resources and potentially degrading memory quality. The staleness window ensures consolidation stays focused on memories that are actively used.
