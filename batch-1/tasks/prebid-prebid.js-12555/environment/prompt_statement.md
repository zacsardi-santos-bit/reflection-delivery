I'm adding an analytics adapter for the R2B2 advertising platform to Prebid.js and right now there's just no way for publishers running R2B2 as a header bidding partner to push auction performance data into R2B2's reporting infra. I want the adapter to hook into the Prebid event system, collect data across the whole auction lifecycle, and send it out in a batched, structured payload to a configurable server endpoint after a short delay.

Config-wise it needs a domain at minimum, and if the domain is missing I want it to log a warning and disable itself rather than silently doing nothing. It should also take optional params for a config identifier, a config version, and a custom server hostname override.

It's gotta track the full set of events: auction start/init, bid requests, bid responses, no-bids, timeouts, bidder completion (bidder done), auction end, bid won, set targeting, ad render success and render failure, stale render, and viewability. Each event gets serialized into its own compact, well-scoped data structure carrying just the relevant auction and bid info.

A couple edge cases matter to me. If a bid-related event fires but there's no auction that was initialized for it, I want an error reported saying no auction context was available. Auctions that end without any bidder requests shouldn't emit an auction-end report at all. Multiple timeout events for the same bidder plus ad unit combo should just be counted, not duplicated as separate entries. And when both an ad render event and a viewability event fire for the same bid, record the elapsed time between them.

Without this, R2B2 publishers on Prebid can't get at their detailed header bidding analytics through the tools they already use, so this closes that gap.
