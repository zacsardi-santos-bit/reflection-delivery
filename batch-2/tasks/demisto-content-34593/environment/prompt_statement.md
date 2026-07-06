So I'm building out a new streaming integration for Chronicle Backstory, our security platform. Right now we've got an integration that polls for detection alerts on a schedule, but polling adds latency and security folks want alerts as close to real time as possible, so I want a separate module that opens a long-lived connection and receives alerts as they arrive over the streaming API instead.

Couple things this needs to get right. Before it does anything it's gotta validate config, specifically that the service account credentials parse as valid JSON and that the first-fetch time window isn't more than 7 days in the past. If either of those is off I want a clear specific error raised, not some generic crash.

I also need a connectivity test that kicks off a short streaming session and returns a simple success string when things work, or a descriptive message explaining what went wrong if the connection fails or the server hands back an error batch (include the error content from the server in that case).

The core streaming loop needs to auto-reconnect after drops using exponential back-off, and after too many consecutive failures it should raise an error that includes the failure count. Oh and there's a nasty edge case: a stale continuation time sitting in the integration context can make the server reject the connection with a 400, and when that happens the message should make clear the connection was refused due to invalid arguments and include the HTTP status code plus the server response body.

Also need to be able to save sample detection events into the integration context and pull them back out as a list later.

Last thing, API errors need parsing into distinct human-readable messages rather than raw output: non-JSON responses, invalid region configs, permission denials, rate limits, and internal server errors should each produce their own clear message. You can model the paths off the existing integration layout, probably something under `@Integrations` for this new streaming module.
