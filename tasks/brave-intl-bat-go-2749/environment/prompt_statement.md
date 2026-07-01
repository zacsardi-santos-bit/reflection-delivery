I'm trying to migrate our codebase from an older Redis client library to a newer, more modern one that offers a cleaner, context-aware API. Several components — the HTTP rate limiting middleware, the cryptocurrency price client, and the ratios service — all currently use an older connection-pool style Redis client and need to be updated to accept and use the new client type instead.

Right now the middleware package won't even compile because the test file already imports the newer Redis library while the production code still references the old one. I need to update the production code to match. As part of this, the rate limiting middleware's Redis store function should drop its database index parameter since the newer client doesn't need it.

I also noticed some credential-filtering tests use the current real-world time for comparisons, which means they'll start failing as the hardcoded test dates age out. Those tests should use fixed reference times to stay deterministic.
