## Description

Prebid.js should support an analytics adapter for the R2B2 advertising platform, allowing publishers who use R2B2 as a header bidding partner to collect and report detailed auction performance data to their analytics endpoint.

Currently, there is no analytics adapter for R2B2, so publishers have no way to feed auction lifecycle events into R2B2's reporting infrastructure via Prebid.

## Expected Behavior

- Publishers can enable the adapter with at minimum a domain configuration option; if the domain is missing, a clear warning must be logged and the adapter must disable itself.
- The adapter captures and reports all key auction lifecycle events in a structured, batched format sent to the analytics server after a short delay.
- Reported events include: auction initialization, bid requests, bid responses, no-bids, bid timeouts, bidder completion, auction end, bid won, set targeting, ad render success and failure, stale render, and bid viewability.
- Each event type carries a concise, well-defined data payload scoped to the relevant auction and bid information.
- When an auction ends with no active bidder requests, no data is reported for that auction end.
- When a bid-related event arrives without a corresponding auction start, an error is reported indicating the missing auction context.
- Bid timeout events for the same bidder and ad unit are counted rather than reported as duplicates.
- The time between an ad being rendered and becoming viewable is recorded for viewability events.

## Why This Matters

Without this adapter, R2B2 publishers running Prebid cannot access detailed header bidding analytics through their existing R2B2 reporting tools. This integration closes that gap and enables data-driven optimization of programmatic ad configurations.
