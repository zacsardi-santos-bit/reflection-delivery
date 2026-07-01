I'm working on the CockroachDB integration and I'd like to expand the set of metrics it collects. The database exposes a large number of metrics about its background jobs through its monitoring endpoint, but we're currently only capturing a small fraction of them. Background jobs handle things like backups, imports, schema changes, replication, automatic statistics, and TTL cleanup, and it would be really useful to have visibility into whether those jobs are healthy.

Specifically, I'd like the integration to start collecting per-job-type metrics that indicate how many jobs are currently idle, paused, or running, along with counters for successful completions, non-retriable failures, and retriable errors — both during normal execution and during failure or cancellation handling. I also want to collect protected timestamp record metrics per job type, and some global registry metrics like how many jobs were claimed and resumed in each adoption cycle.

There are also a handful of changefeed-specific job metrics that belong alongside the other changefeed metrics but aren't being collected yet.

The integration currently has metric maps that translate between the Prometheus metric names (which use underscores) and the Datadog metric names (which use dots). I need the new metrics to be added to those maps so the integration picks them up automatically.
